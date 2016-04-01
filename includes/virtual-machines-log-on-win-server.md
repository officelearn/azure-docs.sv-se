<properties services="virtual-machines" title="How to Log on to a Virtual Machine Running Windows Server" authors="cynthn" solutions="" manager="timlt" editor="tysonn" />

4. Auf **Verbinden** erstellt und eine Remote Desktop Protocol (RDP-Datei) lädt. Klicken Sie auf **Öffnen** Diese Datei verwenden.

5. Klicken Sie im Fenster Remotedesktop auf **Verbinden** um den Vorgang fortzusetzen.

    ![Stellen Sie eine Verbindung her.](./media/virtual-machines-log-on-win-server/connectpublisher.png)

6. Geben Sie im Fenster Windows-Sicherheit die Anmeldeinformationen für ein Konto auf dem virtuellen Computer, und klicken Sie dann auf **OK**.

    In den meisten Fällen sind dies der Benutzername und das Kennwort des lokalen Kontos, das Sie beim Erstellen des virtuellen Computers angegeben haben. In diesem Fall die Domäne ist der Name des virtuellen Computers, und er eingegeben wurde, als *Vmname*& #92;*Benutzername*.  
    
    Wenn der virtuelle Computer zu einer Domäne in Ihrer Organisation gehört, stellen Sie sicher, dass der Benutzername enthält den Namen der Domäne im Format *Domäne*& #92;*Benutzername*. Das Konto muss außerdem in der Administratorgruppe sein oder über Remotezugriffsrechte auf den virtuellen Computer verfügen.
    
    Wenn der virtuelle Computer ein Domänencontroller ist, geben Sie den Benutzernamen und das Kennwort eines Domänenadministratorkontos für diese Domäne an.

7.  Klicken Sie auf **Ja** überprüft die Identität des virtuellen Computers und anmelden.

    ![Bestätigen Sie die Identität des Computers.](./media/virtual-machines-log-on-win-server/connectverify.png)


