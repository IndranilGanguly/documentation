# ANDROID DOCS

## 1. Google Play In App Update Configuration

To configure google play in app update where the google play will show a page mentioning app update availability, the following configurations are required

In build.gradle(app level) file add the following gradle dependency

```gradle
implementation 'com.google.android.play:app-update:2.1.0'
```

Check for the Latest version for play app-update

In launcher Activity use this code to trigger app updates whenever a new version of app is published in playstore

```java
// required Imports
import com.google.android.play.core.appupdate.AppUpdateInfo;
import com.google.android.play.core.appupdate.AppUpdateManager;
import com.google.android.play.core.appupdate.AppUpdateManagerFactory;
import com.google.android.play.core.install.model.AppUpdateType;
import com.google.android.play.core.install.model.UpdateAvailability;

public class MainActivity extends AppCompatActivity {

    // other lines of code

    AppUpdateManager appUpdateManager;

    @Override
	protected void onCreate(Bundle savedInstanceState) {

        super.onCreate(savedInstanceState);
		setContentView(R.layout.activity_login);

        checkForUpdates();
    }

    private void checkForUpdates() {
        // Returns an intent object that you use to check for an update.
		Task<AppUpdateInfo> appUpdateInfoTask = appUpdateManager.getAppUpdateInfo();

        appUpdateInfoTask.addOnSuccessListener(appUpdateInfo -> {
            if (appUpdateInfo.updateAvailability() == UpdateAvailability.UPDATE_AVAILABLE
					&& appUpdateInfo.isUpdateTypeAllowed(AppUpdateType.IMMEDIATE)){
                        try{
                            appUpdateManager.startUpdateFlowForResult(appUpdateInfo,
                            AppUpdateType.IMMEDIATE, this, 123);
                        } catch (IntentSender.SendIntentException e){
                            throw new RuntimeException(e);
                        }
                    }
        });
    }

    @Override
	protected void onActivityResult(int requestCode, int resultCode,
    @Nullable Intent data) {
		super.onActivityResult(requestCode, resultCode, data);
		if (requestCode == 123) {
			if (resultCode != RESULT_OK) {
				Toast.makeText(this, "Update Failed!!", Toast.LENGTH_SHORT).show();
			}
		}
	}
}
```

## 2. SSL Configuration in Android for Retrofit

For communication with REST API where SSL is configured in backend server, the following configurations are required to be implemented for proper communication with the API

#### 2A. If you have a trusted single chain of certificate or a self signed certificate used in testing purpose then place the certificate **"(.crt)"** file in **"raw"** directory of android project. Then create a custom OkHttpClient class and write the following method.

```java
//required imports

import java.io.InputStream;
import java.security.KeyStore;
import java.security.cert.Certificate;
import java.security.cert.CertificateFactory;
import java.util.concurrent.TimeUnit;
import javax.net.ssl.KeyManagerFactory;
import javax.net.ssl.SSLContext;
import javax.net.ssl.SSLSocketFactory;
import javax.net.ssl.TrustManagerFactory;

public class CustomOkHttpClient {

	public static OkHttpClient getCustomOkHttpClient(Context context){
	

		try{

			CertificateFactory cf = CertificateFactory.getInstance("X.509");
			InputStream caInput = context.getResources().openRawResource(R.raw.certificate);

            // here R.raw.certificate is the certificate.crt file which should be present in raw folder in android project directory

			Certificate ca ;
			try {
				ca = cf.generateCertificate(caInput);
			} finally {
				caInput.close();
			}

			String keyStoreType = KeyStore.getDefaultType();
			KeyStore keyStore = KeyStore.getInstance(keyStoreType);  // Use PKCS12 for PEM files
			keyStore.load(null,"pass".toCharArray());
			keyStore.setCertificateEntry("alias",ca);

            // "pass" is the password used to create the certificate
            // "alias" is the alias used to create the certificate

			
			TrustManagerFactory tmf = TrustManagerFactory.getInstance(TrustManagerFactory.getDefaultAlgorithm());
			tmf.init(keyStore);

			KeyManagerFactory keyManagerFactory = KeyManagerFactory.getInstance(KeyManagerFactory.getDefaultAlgorithm());
			keyManagerFactory.init(keyStore, "pass".toCharArray());

            // "pass" is the password used to create the certificate

			// Create an SSLContext using the TrustManagerFactory
			SSLContext sslContext = SSLContext.getInstance("TLS");
			sslContext.init(null, tmf.getTrustManagers(), null);

			// Create an SSLSocketFactory from the SSLContext

			SSLSocketFactory sslSocketFactory = sslContext.getSocketFactory();

			return new OkHttpClient.Builder()
					.sslSocketFactory(sslSocketFactory)
					.hostnameVerifier((hostname, session) -> true)
					.connectTimeout(100, TimeUnit.SECONDS)
					.readTimeout(100,TimeUnit.SECONDS)
					.build();

		} catch (Exception e){
			throw new RuntimeException(e);
		}
	}
}

```

Then use this class in Retrofit Client for your project

```java
// required imports
import com.google.gson.Gson;
import com.google.gson.GsonBuilder;

import java.util.concurrent.TimeUnit;

import okhttp3.OkHttpClient;
import retrofit2.Retrofit;
import retrofit2.converter.gson.GsonConverterFactory;

public class RetrofitClient {


    public static Retrofit retrofit = null;

    static Gson gson = new GsonBuilder()
            .setLenient()
            .create();

    public static Retrofit getApiClient(Context context) {
        OkHttpClient okHttpClient = CustomOkHttpClient.getCustomOkHttpClient(context);
        
        if (retrofit == null) {
            retrofit = new Retrofit.Builder()
                    .baseUrl("URL")   // URL is the server URL
                    .addConverterFactory(GsonConverterFactory.create(gson))
                    .client(okHttpClient)
                    .build();
        }
        return retrofit;
    }
}
```

#### 2B. If you have a multiple chain of certificate like root certificate, intermediate certificate and server certificate, then name the certificate files **(.pem) files** as certificate_root.pem,certificate_intermediate.pem and certificate_server.pem and place these files in **"raw"** directory of android project. Then create a custom OkHttpClient class and write the following method.

```java
// required imports

import java.io.InputStream;
import java.security.cert.CertificateFactory;
import java.security.cert.X509Certificate;
import java.util.ArrayList;
import java.util.List;
import java.util.concurrent.TimeUnit;

import okhttp3.CertificatePinner;
import okhttp3.OkHttpClient;

public class CustomOkHttpClient {

    public static OkHttpClient getOkHttpClient(Context context) {
        OkHttpClient.Builder clientBuilder = new OkHttpClient.Builder();

        try {
            // Load certificates from raw resources
            List<X509Certificate> certificates = new ArrayList<>();
            certificates.add(getCertificate(context, R.raw.certificate_root));
            certificates.add(getCertificate(context, R.raw.certificate_intermediate));
            certificates.add(getCertificate(context, R.raw.certificate_server));

            /*R.raw.certificate_root, R.raw.certificate_intermediate, R.raw.certificate_server
            are the certificate files certificate_root.pem,certificate_intermediate.pem and certificate_server.pem present inside raw directory*/

            // Pin certificates
            CertificatePinner.Builder certificatePinnerBuilder = new CertificatePinner.Builder();
            for (X509Certificate certificate : certificates) {
                String pin = CertificatePinner.pin(certificate);
                certificatePinnerBuilder.add("domain", pin);
            }

            // here domain is the domain for which the ssl is configured "abc.com"

            CertificatePinner certificatePinner = certificatePinnerBuilder.build();
            clientBuilder.certificatePinner(certificatePinner);

        } catch (Exception e) {
            e.printStackTrace();
        }

        return clientBuilder
                .connectTimeout(100, TimeUnit.SECONDS)
                .readTimeout(100,TimeUnit.SECONDS) // Trust all hostnames for demonstration
                .build();
    }

    private static X509Certificate getCertificate(Context context, int resourceId) throws Exception {
        InputStream inputStream = context.getResources().openRawResource(resourceId);
        CertificateFactory certificateFactory = CertificateFactory.getInstance("X.509");
        return (X509Certificate) certificateFactory.generateCertificate(inputStream);
    }
}
```

Then use this class in Retrofit Client for your project

```java
// required imports
import com.google.gson.Gson;
import com.google.gson.GsonBuilder;

import java.util.concurrent.TimeUnit;

import okhttp3.OkHttpClient;
import retrofit2.Retrofit;
import retrofit2.converter.gson.GsonConverterFactory;

public class RetrofitClient {


    public static Retrofit retrofit = null;

    static Gson gson = new GsonBuilder()
            .setLenient()
            .create();

    public static Retrofit getApiClient(Context context) {
        
        OkHttpClient okHttpClient = CustomOkHttpClient.getOkHttpClient(context);

        
        if (retrofit == null) {
            retrofit = new Retrofit.Builder()
                    .baseUrl("URL") // URL is the server URL
                    .addConverterFactory(GsonConverterFactory.create(gson))
                    .client(okHttpClient)
                    .build();
        }
        return retrofit;
    }
}
```