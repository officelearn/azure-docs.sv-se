<properties writer="kathydav" editor="tysonn" manager="timlt" />

Wenn Sie einen Datenträger, der an einen virtuellen Computer angefügt ist, nicht mehr benötigen, können Sie ihn leicht trennen. Dadurch wird der Datenträger von dem virtuellen Computer entfernt, aber nicht aus dem Speicher. 

Wenn Sie die vorhandenen Daten erneut auf dem Datenträger verwenden möchten, können Sie ihn erneut an denselben virtuellen Computer oder an einen anderen anfügen.  

> [AZURE.NOTE] Es ist nicht möglich, Betriebssystem-Datenträger zu trennen, es sei denn, Sie auch den virtuellen Computer löschen.


## Suchen des Datenträgers

Wenn Sie den Namen des Datenträgers nicht kennen oder diesen vor dem Trennen überprüfen möchten, führen Sie die folgenden Schritte aus.


1. Wenn Sie dies noch nicht getan haben, melden Sie sich bei der [Azure-Portal](http://manage.windowsazure.com).

2. Klicken Sie auf **virtuelle Computer**, klicken Sie auf den Namen des virtuellen Computers, und klicken Sie dann auf **Dashboard**.

3. Unter **Datenträger**, die Tabelle enthält den Namen und Typ aller angefügten Datenträger. Beispielsweise zeigt dieser Bildschirm einen virtuellen Computer mit einer Betriebssystemfestplatte und einem Datenträger:

    ![Datenträger suchen](./media/howto-detach-disk-windows-linux/FindDataDisks.png)


## Trennen des Datenträgers

1. Klicken Sie auf **virtuelle Computer**, klicken Sie auf den Namen des virtuellen Computers, den der Datenträger zu trennen hat, und klicken Sie dann auf **Dashboard**.

2. Klicken Sie auf der Befehlsleiste auf **Trennen des Datenträgers**.

3. Wählen Sie den Datenträger aus, und aktivieren Sie das Kontrollkästchen, um ihn zu trennen.

    ![Details zum Trennen des Datenträgers](./media/howto-detach-disk-windows-linux/DetachDiskDetails.png)

Der Datenträger verbleibt im Speicher, ist jedoch nicht mehr an einen virtuellen Computer angefügt.


