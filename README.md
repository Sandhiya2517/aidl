# Ex.No:2 Develop an android application to implement the AIDL server and client app. The server app hosts a Bound Service and contains the logic to return random colours to its client.The client app calls the service and changes the button's colour within the Main activity.



## AIM:

To Develop an android application to implement the AIDL server and client app. The server app hosts a Bound Service and contains the logic to return random colours to its client.
The client app calls the service and changes the button's colour within the Main activity using AIDL interface in Android Studio.

## EQUIPMENTS REQUIRED:

Android Studio(Min.required Griaffe )

## ALGORITHM:

Step 1: Open Android Stdio and then click on File -> New -> New project.

Step 2: Then type the Application name as CSAIDL and click Next. 

Step 3: Then select the Minimum SDK as shown below and click Next.

Step 4: Then select the Empty Activity and click Next. Finally click Finish.

Step 5: Design layout in activity_main.xml.

Step 6: Display message give in MainActivity file(client/server).

Step 7: Save and run the application.

## PROGRAM:
```
/*
Program to print the client/server services using AIDL”.
Developed by: Sandhiya M
Registeration Number : 212224220086
*/
```
## aidlClient app
### MainActivity.java
```
package com.example.aidlclient;

import android.content.ComponentName;
import android.content.Context;
import android.content.Intent;
import android.content.ServiceConnection;
import android.os.Bundle;
import android.os.IBinder;
import android.os.RemoteException;
import android.widget.Button;
import android.widget.Toast;
import androidx.appcompat.app.AppCompatActivity;
import com.example.aidlserver.IColorService;

public class MainActivity extends AppCompatActivity {

    private IColorService colorService;
    private boolean isBound = false;
    private Button btnChangeColor;

    private ServiceConnection connection = new ServiceConnection() {
        @Override
        public void onServiceConnected(ComponentName name, IBinder service)
        {
            colorService = IColorService.Stub.asInterface(service);
            isBound = true;
            Toast.makeText(MainActivity.this, "Connected",
                    Toast.LENGTH_SHORT).show();
        }

        @Override
        public void onServiceDisconnected(ComponentName name) {
            isBound = false;
            colorService = null;
        }
    };

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        btnChangeColor = findViewById(R.id.btnChangeColor);
        btnChangeColor.setOnClickListener(v -> {
            if (isBound && colorService != null) {
                try {
                    int randomColor = colorService.getRandomColor();
                    btnChangeColor.setBackgroundColor(randomColor);
                } catch (RemoteException e) {
                    Toast.makeText(this, "Error",
                            Toast.LENGTH_SHORT).show();
                }
            } else {
                Toast.makeText(this, "Not connected",
                        Toast.LENGTH_SHORT).show();
            }
        });
    }

    @Override
    protected void onStart() {
        super.onStart();
        // Bind to the server's service
        Intent intent = new Intent("com.example.aidlserver.IColorService");
        intent.setPackage("com.example.aidlserver"); // tells Android which app
        bindService(intent, connection, Context.BIND_AUTO_CREATE);
    }

    @Override
    protected void onStop() {
        super.onStop();
        if (isBound) {
            unbindService(connection);
            isBound = false;
        }
    }
}
```
### activity_main.xml
```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:gravity="center"
    android:orientation="vertical">
    <Button
        android:id="@+id/btnChangeColor"
        android:layout_width="200dp"
        android:layout_height="200dp"
        android:text="Tap me!"
        android:textSize="24sp" />
</LinearLayout>
```
### AndroidManifest.xml
```
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools">

    <queries>
        <package android:name="com.example.aidlserver"/>
    </queries>
    <application
        android:allowBackup="true"
        android:dataExtractionRules="@xml/data_extraction_rules"
        android:fullBackupContent="@xml/backup_rules"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/Theme.Aidlclient">
        <activity
            android:name=".MainActivity"
            android:exported="true"
            android:windowSoftInputMode="adjustResize">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>

</manifest>
```

## aidlserver app
### MainActivity.java
```
package com.example.aidlserver;

import android.os.Bundle;

import androidx.activity.EdgeToEdge;
import androidx.appcompat.app.AppCompatActivity;
import androidx.core.graphics.Insets;
import androidx.core.view.ViewCompat;
import androidx.core.view.WindowInsetsCompat;

public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        EdgeToEdge.enable(this);
        setContentView(R.layout.activity_main);
        ViewCompat.setOnApplyWindowInsetsListener(findViewById(R.id.main), (v, insets) -> {
            Insets systemBars = insets.getInsets(WindowInsetsCompat.Type.systemBars());
            v.setPadding(systemBars.left, systemBars.top, systemBars.right, systemBars.bottom);
            return insets;
        });
    }
}
```
### ColorService.java
```
package com.example.aidlserver;

import android.app.Service;
import android.content.Intent;
import android.os.IBinder;
import android.os.RemoteException;
import android.graphics.Color;
import java.util.Random;

public class ColorService extends Service {

    private final IColorService.Stub binder = new IColorService.Stub() {
        @Override
        public int getRandomColor() throws RemoteException {
            Random random = new Random();
            int red = random.nextInt(256);
            int green = random.nextInt(256);
            int blue = random.nextInt(256);
            return Color.rgb(red, green, blue);
        }
    };

    @Override
    public IBinder onBind(Intent intent) {
        return binder;
    }
}
```
### Android Manifest.xml
```
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools">

    <application
        android:allowBackup="true"
        android:dataExtractionRules="@xml/data_extraction_rules"
        android:fullBackupContent="@xml/backup_rules"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/Theme.Aidlserver">
        <activity
            android:name=".MainActivity"
            android:exported="true"
            android:windowSoftInputMode="adjustResize">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
        <service
            android:name=".ColorService"
            android:exported="true"
            android:enabled="true">
            <intent-filter>
                <action android:name="com.example.aidlserver.IColorService" />
            </intent-filter>
        </service>
    </application>

</manifest>
```

## OUTPUT

### aidlserver App
<img width="1920" height="1200" alt="Screenshot 2026-10-29 105528" src="https://github.com/user-attachments/assets/1c81da6b-fba2-418c-a730-9d5f00dce594" />


### aidlclient app
<img width="1920" height="1200" alt="Screenshot 2026-10-29 105515" src="https://github.com/user-attachments/assets/9afb2497-6705-43bf-8ee0-6eb0f68d31fd" />



## RESULT
Thus, a simple Android application was designed and implemented to establish communication between client and server processes using an AIDL interface in Android Studio, and the application was tested and executed successfully.
