# Handoff

### Snapshots

##### Originating App (iPad)

![iPad](Snapshot_1.png "Originating App")



##### Continuing App (iPhone)

![iPhone](Snapshot_2.png "Continuing App")                    ![iPhone](Snapshot_3.png "Continuing App")



## Prerequisites

-   以相同的iCloud账号登录
-   蓝牙LE 4.0
-   iOS 8.0+
-   相同的Developer Team ID
-   支持指定的Activity Type

## Steps

### 一、添加支持的Activity Type类型

在**info.plist**文件里添加**NSUserActivityTypes**，如

![](ActivityType.png)



### 二、Originating App

#### 1. Start user activity

``` swift
func startUserActivity() { 
    let activity = NSUserActivity(activityType: "com.zte.vpn.addvpn")
    activity.title = "Title"
    activity.userInfo = [key: value]
    self.userActivity = activity
    self.userActivity?.becomeCurrent()
}
```

#### 2. Update user activity

``` swift
func textFieldTextDidChanged(notification: NSNotification) {
	self.userActivity?.needsSave = true
}
```

``` swift
override func updateUserActivityState(activity: NSUserActivity) {
  // called，activity.userinfo is clear
	activity.addUserInfoEntriesFromDictionary([key: value])
	super.updateUserActivityState(activity)
}
```

#### 3. Stop user activity

``` swift
func stopUserActivity() {
	self.userActivity?.invalidate()
}
```

#### 4. Versioning (Optional)

``` swift
func application(application: UIApplication, didUpdateUserActivity userActivity: NSUserActivity) {
    userActivity.addUserInfoEntriesFromDictionary([versionKey: versionValue])
}
```



### 三、Continuing App

#### 5. Receive user activity

``` swift
func application(application: UIApplication, willContinueUserActivityWithType userActivityType: String) -> Bool {
    return false
}

func application(application: UIApplication, continueUserActivity userActivity: NSUserActivity, restorationHandler: ([AnyObject]?) -> Void) -> Bool {
    let controller = ...
    controller.restoreUserActivityState(userActivity)
  	or 
  	restorationHandler(controller)
  	return true
}

func application(application: UIApplication, didFailToContinueUserActivityWithType userActivityType: String, error: NSError) {
    if error.code != NSUserCancelledError {
        let alert = ...
        alert.show()
    }
}
```

#### 6. Restore user activity

``` swift
override func restoreUserActivityState(activity: NSUserActivity) {
	//Padding, such as
    self.textField.text = activity.userInfo[key] as? String
}
```



## References

*   [NSUserActivity Class Reference](https://developer.apple.com/library/ios/documentation/Foundation/Reference/NSUserActivity_Class/index.html#//apple_ref/doc/uid/TP40014322)
*   [NSUserActivityDelegate Protocol Reference](https://developer.apple.com/library/ios/documentation/AppKit/Reference/NSUserActivityDelegate_Protocol/index.html#//apple_ref/doc/uid/TP40014337)
*   [UIApplicationDelegate Protocol Reference](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIApplicationDelegate_Protocol/index.html#//apple_ref/doc/uid/TP40006786)
*   [Handoff Programming Guide](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/Handoff/HandoffFundamentals/HandoffFundamentals.html#//apple_ref/doc/uid/TP40014338)
*   iOS 8 by Tutorials