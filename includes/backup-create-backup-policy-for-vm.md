## Definiera en princip för säkerhetskopiering

En princip för säkerhetskopiering definierar en matris över när ögonblicksbilder av data tas och hur länge de kvarhålls. När du definierar en princip för säkerhetskopiering av en VM, kan du utlösa ett säkerhetskopieringsjobb *en gång per dag*. När du skapar en ny princip, tillämpas den för valvet. Gränssnittet för säkerhetskopieringsprincipen ser ut så här:

![Princip för säkerhetskopiering](./media/backup-create-policy-for-vms/backup-policy.png)

Så här skapar du en princip:

1. Ange ett namn för **Principnamn**.

2. Ögonblicksbilder av data kan tas med dags- eller veckointervall. Använd menyn **Säkerhetskopieringsfrekvens** för att välja om ögonblicksbilder av data ska tas dagligen eller veckovis.

    - Använd den markerade kontrollen för att välja tid på dygnet för ögonblicksbilden, om du väljer dagsintervall. Om du vill ändra timme så avmarkerar du timmen och väljer en ny timme.

    ![Princip för daglig säkerhetskopiering](./media/backup-create-policy-for-vms/backup-policy-daily.png) <br/>

    - Använd de markerade kontrollerna för att välja dag i veckan och tid på dygnet att ta ögonblicksbilden, om du väljer veckointervall. Välj en eller flera dagar i dagmenyn. Välj en timme i timmenyn. Om du vill ändra timme så avmarkerar du timmen och väljer en ny timme.

    ![Princip för veckovis säkerhetskopiering](./media/backup-create-policy-for-vms/backup-policy-weekly.png)

3. Som standard är alla alternativ för **Kvarhållningsintervall** markerade. Avmarkera alla begränsningar i kvarhållningsintervallet som du inte vill använda. Ange sedan de intervall som ska användas.

    Månatliga och årliga kvarhållningsintervall låter dig ange ögonblicksbilderna baserat på veckovisa eller dagliga inkrement.

    >[AZURE.NOTE] Ett säkerhetskopieringsjobb körs en gång per dag när du skyddar en VM. Säkerhetskopieringen körs vid samma tidpunkt för varje kvarhållningsintervall.

4. När du har angett alla alternativ för principen överst på bladet, klickar du på **Spara**.

    Den nya principen tillämpas omedelbart på valvet.



<!--HONumber=Jun16_HO2-->


