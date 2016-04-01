

1. In der [klassischen Azure-Portal](https://manage.windowsazure.com/), klicken Sie auf die **Daten** Registerkarte, und klicken Sie dann auf die **Registrierungen** Tabelle. 

    ![](./media/mobile-services-update-registrations-script/mobile-portal-data-tables-registrations.png)

2. In **Registrierungen**, klicken Sie auf die **Skript** Registerkarte, und wählen Sie **Einfügen**.
   
    ![](./media/mobile-services-update-registrations-script/mobile-insert-script-registrations.png)

    Die Funktion angezeigt, die aufgerufen wird, wenn ein Einfügevorgang in erfolgt die **Registrierungen** Tabelle.

3. Ersetzen Sie die Einfügefunktion durch den folgenden Code, und klicken Sie dann auf **Speichern**:

        function insert(item, user, request) {
            var registrationTable = tables.getTable('Registrations');
            registrationTable
                .where({ handle: item.handle })
                .read({ success: insertChannelIfNotFound });
            function insertChannelIfNotFound(existingRegistrations) {
                if (existingRegistrations.length > 0) {
                    request.respond(200, existingRegistrations[0]);
                } else {
                    request.execute();
                }
            }
        }

   Auf diese Weise wird ein neues Einfügeskript registriert, das die Registrierungsinformationen in der neuen Tabelle speichert.



