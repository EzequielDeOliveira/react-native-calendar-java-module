# React Native Calendar Native Module

This repository shows how to create native modules to React Native creating an event in the smartphone's calendar.

### CalendarModule.java
The [CalendarModule.java](./android/app/src/main/java/com/calendarnativemodulejava/CalendarModule.java) is the core of the native module.
It contains the logic to receive data from the Javascript side, open the calendar, and fill the fields.

After extending ***ReactContextBaseJavaModule*** is necessary to override the function getName to return the component's name to be imported on the Javascript side.

To create a callable function to React Native, use ***@ReactMethod*** after the method.

```
public class CalendarModule extends ReactContextBaseJavaModule {
    ReactApplicationContext context;

    CalendarModule(ReactApplicationContext context) {
        this.context = context;
    }

    @Override
    public String getName() {
        return "CalendarModule";
    }

    @ReactMethod
    public void createCalendarEvent(String name, String location) {
        Log.d("CalendarModule", "Create event called with name: " + name
                + " and location: " + location);
        Intent intent = new Intent(Intent.ACTION_EDIT)
            .setData(CalendarContract.Events.CONTENT_URI)
            .putExtra(CalendarContract.Events.TITLE, name)
            .putExtra(CalendarContract.Events.EVENT_LOCATION, location)
            .putExtra(CalendarContract.EXTRA_EVENT_BEGIN_TIME, System.currentTimeMillis())
            .putExtra(CalendarContract.EXTRA_EVENT_END_TIME, System.currentTimeMillis() + (60 * 60 * 1000))
            .setFlags(Intent.FLAG_ACTIVITY_NEW_TASK);

        context.startActivity(intent);
    }
}
```

### CalendarPackage.java
The [CalendarPackage.java](/android/app/src/main/java/com/calendarnativemodulejava/CalendarPackage.java) must expose the created module to the react native 
modules, extending ***ReactPackage*** and overriding ***createViewManager*** to native components and ***createNativeModules*** to modules.

```
public class CalendarPackage implements ReactPackage {

    @Override
    public List<ViewManager> createViewManagers(ReactApplicationContext reactContext){
        return Collections.emptyList();
    }

    @Override
    public List<NativeModule> createNativeModules(ReactApplicationContext reactContext) {
        List<NativeModule> modules = new ArrayList<>();

        modules.add(new CalendarModule(reactContext));

        return modules;
    }
}
```

### MainApplication.java
In the ***MainApplication.java***, in the function [getPackages](https://github.com/EzequielDeOliveira/react-native-calendar-java-module/blob/7df0c1241c6f19a1331fb1970ea1fae71967ddf7/android/app/src/main/java/com/calendarnativemodulejava/MainApplication.java#L28) it's time to import our package.

```
@Override
protected List<ReactPackage> getPackages() {
    @SuppressWarnings("UnnecessaryLocalVariable")
    List<ReactPackage> packages = new PackageList(this).getPackages();
    // Packages that cannot be autolinked yet can be added manually here, for example:
    packages.add(new CalendarPackage());
    return packages;
}
```

### CalendarModule.js
The file [CalendarModule.js](./CalendarModule.js) handles the native module and exposes the function on the Javascript side.
```
import { NativeModules } from 'react-native';
const { CalendarModule } = NativeModules;
export default CalendarModule;
```
To use import the function and pass the title of the event and the location, like this:
```
CalendarModule.createCalendarEvent('testName', 'testLocation');
```
### Result
![ezgif com-gif-maker](https://user-images.githubusercontent.com/37127457/167336147-8b07ef94-5afe-4eef-9a85-9be5093b9425.gif)

** This project is based on the [React Native's documentation](https://reactnative.dev/docs/native-modules-android)



