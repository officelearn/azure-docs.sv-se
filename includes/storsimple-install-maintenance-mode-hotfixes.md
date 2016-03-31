<!--author=SharS last changed: 9/17/15-->

#### So installieren Sie Wartungsmodus-Hotfixes über Windows PowerShell für StorSimple

> [AZURE.IMPORTANT] Im Wartungsmodus befindet müssen Sie den Hotfix zunächst auf einen Controller und dann auf den anderen Controller anwenden.

1. Versetzen Sie das Gerät in den Wartungsmodus. Finden Sie unter [Schritt2: Geben Sie Wartungsmodus](storsimple-update-device.md#step2) Hinweise in den Wartungsmodus.

2. Geben Sie zum Anwenden des Hotfixes Folgendes ein:

     `Start-HcsHotfix` 

3. Geben Sie bei entsprechender Aufforderung den Pfad zur Netzwerkfreigabe mit den Hotfix-Dateien ein.

4. Sie werden aufgefordert, diesen Schritt zu bestätigen. Typ **Y** mit der Installation des Hotfixes fortzufahren.

5. Nachdem Sie den Hotfix auf einem Controller installiert haben, melden Sie sich bei dem anderen Controller an. Wenden Sie den Hotfix wie beim vorherigen Controller an.

6. Nachdem alle Hotfixes angewendet wurden, beenden Sie den Wartungsmodus. Finden Sie unter [Schritt 4: Beenden des Wartungsmodus](storsimple-update-device.md#step4) Anweisungen.


