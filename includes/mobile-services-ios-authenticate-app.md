* Öffnen Sie **QSTodoListViewController.m** und fügen Sie die folgende Methode hinzu. Änderung _Facebook_ auf _Microsoftaccount_, _twitter_, _Google_, oder _Windowsazureactivedirectory_ Wenn Sie Facebook nicht als Identitätsanbieter verwenden.

```
        - (void) loginAndGetData
        {
            MSClient *client = self.todoService.client;
            if (client.currentUser != nil) {
                return;
            }

            [client loginWithProvider:@"facebook" controller:self animated:YES completion:^(MSUser *user, NSError *error) {
                [self refresh];
            }];
        }
```

* Ersetzen Sie `[self refresh]` in `viewDidLoad` durch Folgendes:

```
        [self loginAndGetData];
```

* Drücken Sie  **Ausführen** an die app zu starten, und melden Sie sich. Nach der Anmeldung sollten Sie die Todo-Liste anzeigen und Änderungen vornehmen können.


