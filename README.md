# Bottom Navigation - Android

## Purpose:
The purpose of this code base is to explore the different ways that you can add a Bottom Navigation Bar to an app. The two different implementations that are used in this codebase is Jetpack's Navigation Componenets add the OnNavigationItemSelectedListener method.

## BottomNavigationView:
The naming of the this UI componenet is inconsistent across the documentaion and APIs (unless I am missing something). It is referred to as BottomNavigationView, but as it is also a menu, it can be referred to as BottomNavigatioMenu. This is noticeable in the XML files you work with to build out this functionality (app:menu, header type).  

You need to create an XML file for the bottom navigation view. This will be saved into a menu folder in your file directory. The XML file contains items types that corresponds to the tabs along the view.

```
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto">

    <item
        android:id="@+id/home"
        android:title="Home"
        app:showAsAction="always"
        android:orderInCategory="1"
        android:icon="@drawable/nav_bar_home_foreground" />

    <item
        android:id="@+id/settings"
        android:title="Settings"
        app:showAsAction="always"
        android:orderInCategory="2"
        android:icon="@drawable/nav_bar_settings_foreground" />

</menu>
```

Once you have created your bottom navigation view xml file, you can implement it into your Activities XMLs as you would any other view element.

```
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">
    
    <com.google.android.material.bottomnavigation.BottomNavigationView
        android:id="@+id/bottomNavigationView"
        android:layout_width="match_parent"
        android:layout_height="75dp"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintHorizontal_bias="0.5"
        app:layout_constraintStart_toStartOf="parent"
        app:menu="@menu/bottom_nav_menu"/>

</androidx.constraintlayout.widget.ConstraintLayout>
```

## Jetpack
You are able to use the Jetpack Navigation Components to manage your BottomNavigationView functionality. Jetpack Navigation Graphs is destination and fragment focused. In your MainActivity, you will need a FragmentContainerView that will hold the fragments the user will navigation through. There are three properties that the Navigation Graph depends on in order to function correctly. 

android:name="androidx.navigation.fragment.NavHostFragment"
app:defaultNavHost="true"
app:navGraph="@navigation/nav_graph"

```
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <androidx.fragment.app.FragmentContainerView
        android:id="@+id/nav_host_fragment_container"
        android:name="androidx.navigation.fragment.NavHostFragment"
        android:layout_width="match_parent"
        android:layout_height="0dp"

        app:layout_constraintBottom_toTopOf="@+id/bottomNavigationView"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintHorizontal_bias="0.5"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent"

        app:defaultNavHost="true"
        app:navGraph="@navigation/nav_graph"
        />


    <com.google.android.material.bottomnavigation.BottomNavigationView
        android:id="@+id/bottomNavigationView"
        android:layout_width="match_parent"
        android:layout_height="75dp"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintHorizontal_bias="0.5"
        app:layout_constraintStart_toStartOf="parent"
        app:menu="@menu/bottom_nav_menu"/>

</androidx.constraintlayout.widget.ConstraintLayout>
```

##### Navigating to a destination:
Navigation is handeled using a NavController. This object manages app navigation with a NavHost (each host as its own controller). You need to get the navcontroller for the host that you are interacting with (findFragmentById). As we are using a BottomNavigationView, we need to setup the navigationController with the bottomNavigationView.

```
NavHostFragment navHostFragment =
        (NavHostFragment) supportFragmentManager.findFragmentById(R.id.nav_host_fragment);
NavigationUI.setupWithNavController(bottomNavigationView, navHostFragment.getNavController());
```

##### Navigation Graph:
You setup your Navigation Graph as you would without a BottomNavigationView. Ensure that your IDs are consistent across the fragmentId (in the navgraph), and bottom_nav_menu items id.


## OnNavigationItemSelectedListener
The more traditional way (I think) is to implement a BottomNavigationView using the OnNavigationItemSelectedListener. You create and implement your bottom_nav_menu.xml and your FragmentContainer/FrameLayout as you usually would. This approach uses the FragmentManager and FragmentTransaction objects to manage adding and replacing fragments based on user interaction.

```
package com.example.bottomnavigationjetpack;

import androidx.annotation.NonNull;
import androidx.appcompat.app.AppCompatActivity;
import androidx.fragment.app.FragmentManager;
import androidx.fragment.app.FragmentTransaction;
import androidx.navigation.fragment.NavHostFragment;
import androidx.navigation.ui.NavigationUI;

import android.app.Fragment;
import android.os.Bundle;
import android.provider.Settings;
import android.util.Log;
import android.view.MenuItem;
import android.view.View;

import com.google.android.material.bottomnavigation.BottomNavigationView;
import com.google.android.material.navigation.NavigationView;

public class MainActivity extends AppCompatActivity {

    HomeFragment homeFragment;
    SettingsFragment settingsFragment;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        homeFragment = new HomeFragment();
        settingsFragment = new SettingsFragment();

        BottomNavigationView bottomNavigationView = findViewById(R.id.bottomNavigationView);
        bottomNavigationView.setOnNavigationItemSelectedListener(new BottomNavigationView.OnNavigationItemSelectedListener() {
            @Override
            public boolean onNavigationItemSelected(@NonNull MenuItem item) {
                FragmentManager fragmentManager = getSupportFragmentManager();
                FragmentTransaction fragmentTransaction = fragmentManager.beginTransaction();

                switch (item.getItemId()) {
                    case R.id.home:
                        fragmentTransaction.replace(R.id.flFragment, homeFragment);
                        fragmentTransaction.commit();
                        break;
                    case R.id.settings:
                        fragmentTransaction.replace(R.id.flFragment, settingsFragment);
                        fragmentTransaction.commit();
                        break;
                }
                return false;
            }
        });
    }
}
```
