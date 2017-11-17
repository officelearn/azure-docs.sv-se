Skapa en enhetsidentitet för den simulerade enheten så att den kan kommunicera med din IoT-hubb. Eftersom IoT-gränsenheterna fungerar och kan hanteras annorlunda än typisk IoT-enheter, kan du ange detta ska vara en insticksenhet för IoT från början. 

1. Navigera till din IoT-hubb i Azure-portalen.
1. Välj **IoT kant (förhandsgranskning)** Välj **lägga till IoT-enhet**.

   ![Lägg till IoT-enhet](./media/iot-edge-register-device/add-device.png)

1. Ge den simulerade enheten ett unikt enhets-ID.
1. Välj **spara** lägga till enheten.
1. Välj den nya enheten i listan över enheter.
1. Kopiera värdet för **anslutningssträngen – primärnyckel** och spara den. Så här konfigurerar du IoT kant-körningsmiljön i nästa avsnitt ska du använda det här värdet. 

