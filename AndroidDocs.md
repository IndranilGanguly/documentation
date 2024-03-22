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
