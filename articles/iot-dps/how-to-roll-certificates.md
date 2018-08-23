---
title: Hur du ska distribuera X.509-certifikat i Azure IoT Hub Device Provisioning-tjänsten | Microsoft Docs
description: Hur du ska distribuera X.509-certifikat med Device Provisioning-tjänstinstans
author: wesmc7777
ms.author: wesmc
ms.date: 08/06/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: a8ba667e6af316620d7a8530f29a6640edada13d
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2018
ms.locfileid: "42056763"
---
# <a name="how-to-roll-x509-device-certificates"></a>Hur du ska distribuera X.509-enhetscertifikat

Under livscykeln för din IoT-lösning måste du distribuera certifikat. Två av de viktigaste skälen för löpande certifikat skulle vara en säkerhetsöverträdelse och förfallodatum för certifikat. 

Löpande certifikat är en säkerhetsåtgärd för att skydda datorn i händelse av ett intrång. Som en del av [förutsätter intrång metod](http://download.microsoft.com/download/C/1/9/C1990DBA-502F-4C2A-848D-392B93D9B9C3/Microsoft_Enterprise_Cloud_Red_Teaming.pdf), Microsoft representanter behovet av att reaktiv säkerhetsprocesser ha tillsammans med förebyggande åtgärder. Löpande dina enhetscertifikat ska vara med i dessa processer med security. Den frekvens som du distribuerar dina certifikat beror på säkerhetsbehoven hos din lösning. Kunder med lösningar som inbegriper mycket känsliga data kan distribuera certifikat varje dag, medan andra distribuera sina certifikat varje par år.

Löpande enhetscertifikat måste du uppdatera certifikat som lagras på enheten och IoT-hubben. Därefter kan enheten kan etablera om sig själv med IoT hub med normal [Automatisk etablering](concepts-auto-provisioning.md) med Device Provisioning-tjänsten.


## <a name="obtain-new-certificates"></a>Hämta nya certifikat

Det finns många sätt att hämta nya certifikat för dina IoT-enheter. Dessa inkluderar skaffa certifikat från enheten fabriken, skapa dina egna certifikat och att det finns en tredje part hantera skapandet av certifikat åt dig. 

Certifikat som är signerade av varandra för att bilda en certifikatkedja från ett rot-CA-certifikat till en [lövcertifikat](concepts-security.md#end-entity-leaf-certificate). Ett signeringscertifikat är det certifikat som används för att signera lövcertifikatet i slutet av förtroendekedjan. Ett signeringscertifikat kan vara ett rotcertifikatutfärdarcertifikat eller ett mellanliggande certifikat i certifikatkedja. Mer information finns i [X.509-certifikat](concepts-security.md#x509-certificates).
 
Det finns två sätt att hämta ett signeringscertifikat. Den första metoden, vilket rekommenderas för produktionssystem, är att köpa ett signeringscertifikat från en rotcertifikatutfärdare (CA). Det här sättet länkar security till en betrodd källa. 

Det andra sättet är att skapa dina egna X.509-certifikat med ett verktyg som OpenSSL. Den här metoden är bra för att testa X.509-certifikat, men innehåller några garantier kring säkerhet. Vi rekommenderar att du bara använda den här metoden för att testa om du har förberett att fungera som en egen CA-provider.
 

## <a name="roll-the-certificate-on-the-device"></a>Distribuera certifikatet på enheten

Certifikat på en enhet ska alltid lagras på en säker plats som en [maskinvarusäkerhetsmodul (HSM)](concepts-device.md#hardware-security-module). Hur du distribuerar enhetscertifikat beror på hur de har skapats och installerade på enheterna i första hand. 

Om du har fått ditt certifikat från en tredje part måste du se över hur de distribuera sina certifikat. Processen kan ingå i en ordning med dem eller det kan vara en separat tjänst som de erbjuder. 

Om du hanterar din egen enhetscertifikat, måste du skapa din egen pipeline för att uppdatera certifikat. Kontrollera att både gamla och nya löv-certifikat har samma nätverksnamn (CN). Genom att använda samma CN enheten kan etablera om själva utan att skapa en duplicerad registreringspost.


## <a name="roll-the-certificate-in-the-iot-hub"></a>Distribuera certifikatet i IoT hub

Enhetens certifikat kan läggas till manuellt till en IoT hub. Certifikatet kan också automatiseras med hjälp av en Device Provisioning-tjänstinstans. I den här artikeln förutsätter vi att en Device Provisioning-tjänstinstans som används för automatisk etablering.

När en enhet har etablerats via automatisk etablering, den startas upp och kontaktar etableringstjänsten. Etableringstjänsten svarar genom att utföra identitetskontroll innan du skapar en enhetsidentitet i en IoT hub med enhetens lövcertifikatet som autentiseringsuppgifter. Etableringstjänsten meddelar sedan enheten vilken IoT-hubb tilldelas och enheten sedan dess lövcertifikatet använder för att autentisera och ansluta till IoT hub. 

När ett nytt lövcertifikat har återställts till enheten, kan den inte längre ansluta till IoT hub eftersom den använder ett nytt certifikat för att ansluta. IoT hub kan endast identifiera enheten med det gamla certifikatet. Resultatet av anslutningsförsök till enheten kommer att felmeddelandet ”obehörig” anslutning. För att lösa det här felet måste du uppdatera registreringspost för enheten att kompensera för enhetens nya lövcertifikatet. Etableringstjänsten kan sedan uppdatera IoT Hub device registerinformationen efter behov när enheten är nätverkskonfigurationsinställningar. 

Ett möjligt undantag till den här anslutningsfel är ett scenario där du har skapat en [Registreringsgruppen](concepts-service.md#enrollment-group) för din enhet i etableringstjänsten. I det här fallet om du inte löpande rot- eller mellanliggande certifikat i enhetens certifikatkedja, identifieras sedan enheten om det nya certifikatet ingår i förtroendekedjan som definierats i registreringsgruppen. Om det här scenariot uppstår som en reaktion på en säkerhetsöverträdelse, bör du minst svartlista specifik enhet certifikaten i gruppen som anses vara brott mot. Mer information finns i [Svartlista specifika enheter i en registreringsgrupp](https://docs.microsoft.com/en-us/azure/iot-dps/how-to-revoke-device-access-portal#blacklist-specific-devices-in-an-enrollment-group).

Uppdaterar poster för registrering för certifikat som återkallas görs på den **hantera registreringar** sidan. Följ dessa steg för att komma åt sidan:

1. Logga in på den [Azure-portalen](https://portal.azure.com) och navigera till den IoT Hub Device Provisioning Service-instans som har registreringsposten för din enhet.

2. Klicka på **Hantera registreringar**.

    ![Hantera registreringar](./media/how-to-roll-certificates/manage-enrollments-portal.png)


Hur du hanterar uppdaterar registreringsposten beror på om du använder enskilda registreringar eller gruppregistreringar. Rekommenderade procedurerna varierar också beroende på om du rullar certifikat på grund av en säkerhetsbrist eller förfallodatum för certifikat. I följande avsnitt beskrivs hur du hanterar de här uppdateringarna.


## <a name="individual-enrollments-and-security-breaches"></a>Enskilda registreringar och säkerhetsbrott

Om du rullar certifikat som svar på en säkerhetsöverträdelse, bör du använda följande metod som tar bort det aktuella certifikatet direkt:

1. Klicka på **enskilda registreringar**, och klicka på posten registrerings-ID i listan. 

2. Klicka på den **ta bort aktuell certifikat** knappen och sedan klicka på mappikonen för att välja det nya certifikatet som ska laddas upp för registreringsposten. Klicka på **spara** när du är klar.

    Dessa steg ska utföras för det primära och sekundära certifikatet om båda skulle skadas.

    ![Hantera enskilda registreringar](./media/how-to-roll-certificates/manage-individual-enrollments-portal.png)

3. När komprometterade certifikatet har tagits bort från etableringstjänsten, navigera till din IoT-hubb och ta bort enhetsregistreringen som är associerade med det komprometterade certifikatet.     

    ![Ta bort IoT hub-enhetsregistrering](./media/how-to-roll-certificates/remove-hub-device-registration.png)


## <a name="individual-enrollments-and-certificate-expiration"></a>Enskilda registreringar och förfallodatum för certifikat

Om du rullar certifikat för att hantera förfallodatum för certifikat, bör du använda sekundära certifikatkonfigureringen på följande sätt för att minska stopptiden för enheter som försöker etablera.

Senare när sekundärt certifikat också närmar sig förfallodatum och behöver återställas, du kan rotera till med hjälp av den primära konfigurationen. Rotera mellan primära och sekundära certifikat på så vis minskar avbrottstiden för enheter som försöker etablera.


1. Klicka på **enskilda registreringar**, och klicka på posten registrerings-ID i listan. 

2. Klicka på **sekundärt certifikat** och sedan klicka på mappikonen för att välja det nya certifikatet som ska laddas upp för registreringsposten. Klicka på **Spara**.

    ![Hantera enskilda registreringar med sekundärt certifikat](./media/how-to-roll-certificates/manage-individual-enrollments-secondary-portal.png)

3. Senare när det primära certifikatet har upphört att gälla, gå tillbaka och ta bort primär certifikatet genom att klicka på den **ta bort aktuell certifikat** knappen.

## <a name="enrollment-groups-and-security-breaches"></a>Registrering av grupper och säkerhetsbrott

Om du vill uppdatera en gruppregistrering som svar på en säkerhetsöverträdelse, bör du använda någon av följande metoder som tar bort den aktuella rotcertifikatutfärdare eller mellanliggande certifikat omedelbart.

#### <a name="update-compromised-root-ca-certificates"></a>Uppdatera komprometterade rot-CA-certifikat

1. Klicka på den **certifikat** fliken för Device Provisioning-tjänstinstans.

2. Klicka på det komprometterade certifikatet i listan och klicka sedan på den **ta bort** knappen. Bekräfta borttagningen genom att ange certifikatets namn och klicka på **OK**. Upprepa proceduren för alla komprometterade certifikat.

    ![Ta bort rot-CA-certifikat](./media/how-to-roll-certificates/delete-root-cert.png)

3. Följ stegen som beskrivs i [konfigurera verifierat certifikat](how-to-verify-certificates.md) att lägga till och verifiera nya rot-CA-certifikat.

4. Klicka på den **hantera registreringar** för Device Provisioning-tjänstinstans och på den **Registreringsgrupper** lista. Klicka på registrering namn i listan.

5. Klicka på **certifikatutfärdarcertifikat**, och välj det nya rot-CA-certifikatet. Klicka sedan på **Spara**. 

    ![Välj det nya rotcertifikatet för Certifikatutfärdaren](./media/how-to-roll-certificates/select-new-root-cert.png)

6. När komprometterade certifikatet har tagits bort från etableringstjänsten, navigera till den länkade IoT-hubben som innehåller de komprometterade enhetsregistreringar och ta bort registreringarna som är associerade med det komprometterade certifikatet.

    ![Ta bort IoT hub-enhetsregistrering](./media/how-to-roll-certificates/remove-hub-device-registration.png)


#### <a name="update-compromised-intermediate-certificates"></a>Uppdatera komprometterade mellanliggande certifikat

1. Klicka på **Registreringsgrupper**, och klicka sedan på namnet på i listan. 

2. Klicka på **mellanliggande certifikat**, och **ta bort aktuell certifikat**. Klicka på mappikonen för att navigera till det nya mellanliggande certifikatet som ska laddas upp för registreringsgruppen. Klicka på **spara** när du är klar. Dessa steg ska utföras för både det primära och sekundära certifikatet, om båda skulle skadas.

    Den här nya mellanliggande certifikat ska signeras av en verifierad rotcertifikatutfärdarcertifikat som redan har lagts till etableringstjänsten. Mer information finns i [X.509-certifikat](concepts-security.md#x509-certificates).

    ![Hantera enskilda registreringar](./media/how-to-roll-certificates/enrollment-group-delete-intermediate-cert.png)


3. När komprometterade certifikatet har tagits bort från etableringstjänsten, navigera till den länkade IoT-hubben som innehåller enhetsregistreringen och ta bort registreringen som är associerade med det komprometterade certifikatet.

    ![Ta bort IoT hub-enhetsregistrering](./media/how-to-roll-certificates/remove-hub-device-registration.png)


## <a name="enrollment-groups-and-certificate-expiration"></a>Registrering av grupper och förfallodatum för certifikat

Om du kommer att börja med certifikat för att hantera förfallodatum för certifikat, bör du använda sekundära certifikatkonfigureringen på följande sätt att se till att utan avbrott för enheter som försöker etablera.

Senare när sekundärt certifikat också närmar sig förfallodatum och behöver återställas, du kan rotera till med hjälp av den primära konfigurationen. Rotera mellan primära och sekundära certifikat på så sätt säkerställer utan avbrott för enheter som försöker etablera. 

#### <a name="update-expiring-root-ca-certificates"></a>Uppdatera upphör rot-CA-certifikat

1. Följ stegen som beskrivs i [konfigurera verifierat certifikat](how-to-verify-certificates.md) att lägga till och verifiera nya rot-CA-certifikat.

2. Klicka på den **hantera registreringar** för Device Provisioning-tjänstinstans och på den **Registreringsgrupper** lista. Klicka på registrering namn i listan.

3. Klicka på **CA-certifikat**, och välj det nya rot-CA-certifikatet under den **sekundärt certifikat** konfiguration. Klicka sedan på **Spara**. 

    ![Välj det nya rotcertifikatet för Certifikatutfärdaren](./media/how-to-roll-certificates/select-new-root-secondary-cert.png)

4. Senare när det primära certifikatet har upphört att gälla, klickar du på den **certifikat** fliken för Device Provisioning-tjänstinstans. Klicka på det utgångna certifikatet i listan och klicka sedan på den **ta bort** knappen. Bekräfta borttagningen genom att ange certifikatets namn och klickar på **OK**.

    ![Ta bort rot-CA-certifikat](./media/how-to-roll-certificates/delete-root-cert.png)



#### <a name="update-expiring-intermediate-certificates"></a>Uppdatera upphör mellanliggande certifikat


1. Klicka på **Registreringsgrupper**, och klicka på namnet på i listan. 

2. Klicka på **sekundärt certifikat** och sedan klicka på mappikonen för att välja det nya certifikatet som ska laddas upp för registreringsposten. Klicka på **Spara**.

    Den här nya mellanliggande certifikat ska signeras av en verifierad rotcertifikatutfärdarcertifikat som redan har lagts till etableringstjänsten. Mer information finns i [X.509-certifikat](concepts-security.md#x509-certificates).

   ![Hantera enskilda registreringar med sekundärt certifikat](./media/how-to-roll-certificates/manage-enrollment-group-secondary-portal.png)

3. Senare när det primära certifikatet har upphört att gälla, gå tillbaka och ta bort primär certifikatet genom att klicka på den **ta bort aktuell certifikat** knappen.


## <a name="reprovision-the-device"></a>Etablera om enheten

När certifikatet har slagits på både enheten och Enhetsetableringstjänsten enheten kan etablera om själva genom att kontakta Device Provisioning-tjänsten. 

Ett enkelt sätt att programming enheter att etablera om är att programmera enheten att kontakta etableringstjänsten gå igenom etablering flödet om enheten får felmeddelandet ”obehörig” försöker ansluta till IoT hub.

Ett annat sätt är för både gammalt och nya certifikat att vara giltigt för en kort överlappning och använda IoT-hubben för att skicka ett kommando till enheter som de registrerar sig igen via etableringstjänsten att uppdatera sin anslutning IoT Hub. Eftersom varje enhet kan bearbeta kommandon på olika sätt, kommer du behöva programmera din enhet för att vet vad de ska göra när kommandot anropas. Det finns flera sätt du kan kommandot enheten via IoT Hub och vi rekommenderar att du använder [direkta metoder](../iot-hub/iot-hub-devguide-direct-methods.md) eller [jobb](../iot-hub/iot-hub-devguide-jobs.md) att starta processen.

När reprovisioning är klar, kommer enheter att kunna ansluta till IoT Hub med sina nya certifikat.


## <a name="blacklist-certificates"></a>Svartlistat certifikat

Du kan behöva svartlista ett certifikat som svar på en säkerhetsöverträdelse. Inaktivera registreringsposten för mål-/ enhetscertifikat blacklist ett certifikat. Mer information finns i godkänna enheter i den [hantera avregistrering](how-to-revoke-device-access-portal.md) artikeln.

När ett certifikat ingår som en del av en post för inaktiverad registrering, försök att registrera med en IoT hub med certifikat misslyckas även om den är aktiverad som en del av en annan post för registrering.
 



## <a name="next-steps"></a>Nästa steg

- Läs mer om X.509-certifikat i Device Provisioning-tjänsten i [säkerhet](concepts-security.md) 
- Läs om hur du gör bevis tillgång för X.509 CA-certifikat med Azure IoT Hub Device Provisioning-tjänsten i [hur du verifierar certifikat](how-to-verify-certificates.md)
- Läs om hur du använder portalen för att skapa en grupp för registrering i [hantera enhetsregistreringar med Azure-portalen](how-to-manage-enrollments.md).










