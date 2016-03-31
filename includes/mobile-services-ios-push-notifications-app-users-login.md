
Als Nächstes müssen Sie die Methode zur Registrierung von Pushbenachrichtigungen ändern, um sicherzustellen, dass der Benutzer vor einem Registrierungsversuch authentifiziert wird.

1. In **QSAppDelegate.m**, entfernen Sie die Implementierung des **DidFinishLaunchingWithOptions** vollständig.

2. Öffnen Sie **QSTodoListViewController.m** und fügen Sie den folgenden Code am Ende der **ViewDidLoad** Methode:

```
// Register for remote notifications
[[UIApplication sharedApplication] registerForRemoteNotificationTypes:
UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
```


