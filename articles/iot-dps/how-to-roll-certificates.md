---
title: Roll X.509-certifikat i Azure IoT Hub Device Provisioning Service
description: Så här rullar du X.509-certifikat med DPS-instansen (Device Provisioning Service)
author: wesmc7777
ms.author: wesmc
ms.date: 08/06/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 4d5ddb229cd6a41235990437bc0f8db08e3381ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74974895"
---
# <a name="how-to-roll-x509-device-certificates"></a>Så här rullar du X.509-enhetscertifikat

Under livscykeln för din IoT-lösning måste du rulla certifikat. Två av de främsta orsakerna till rullande certifikat skulle vara en säkerhetsöverträdelse och certifikatutgångningar. 

Rullande certifikat är en säkerhetspraxis för att skydda ditt system i händelse av ett brott. Som en del av [Assume Breach Methodology](https://download.microsoft.com/download/C/1/9/C1990DBA-502F-4C2A-848D-392B93D9B9C3/Microsoft_Enterprise_Cloud_Red_Teaming.pdf)förespråkar Microsoft behovet av att ha reaktiva säkerhetsprocesser på plats tillsammans med förebyggande åtgärder. Rullande enhetscertifikat bör inkluderas som en del av dessa säkerhetsprocesser. Hur ofta du rullar dina certifikat beror på säkerhetsbehoven i din lösning. Kunder med lösningar som involverar mycket känsliga data kan rulla certifikat dagligen, medan andra rullar sina certifikat vartannat år.

Rullande enhetscertifikat innebär att uppdatera certifikatet som lagras på enheten och IoT-hubben. Därefter kan enheten återetablera sig med IoT-hubben med normal [automatisk etablering](concepts-auto-provisioning.md) med enhetsetableringstjänsten.


## <a name="obtain-new-certificates"></a>Skaffa nya certifikat

Det finns många sätt att hämta nya certifikat för dina IoT-enheter. Dessa inkluderar att hämta certifikat från enhetsfabriken, generera egna certifikat och låta en tredje part hantera certifikatskapande för dig. 

Certifikat signeras av varandra för att bilda en förtroendekedja från ett rotcertifikatutfärdarcertifikat till ett [lövcertifikat](concepts-security.md#end-entity-leaf-certificate). Ett signeringscertifikat är det certifikat som används för att signera lövcertifikatet i slutet av förtroendekedjan. Ett signeringscertifikat kan vara ett rotcertifikatutfärdarcertifikat eller ett mellanliggande certifikat i förtroendekedjan. Mer information finns i [X.509-certifikat](concepts-security.md#x509-certificates).
 
Det finns två olika sätt att skaffa ett signeringscertifikat. Det första sättet, som rekommenderas för produktionssystem, är att köpa ett signeringscertifikat från en rotcertifikatutfärdarmyndighet (CA). På så sätt kedjor säkerhet ner till en betrodd källa. 

Det andra sättet är att skapa dina egna X.509-certifikat med ett verktyg som OpenSSL. Den här metoden är bra för att testa X.509-certifikat men ger få garantier kring säkerhet. Vi rekommenderar att du bara använder den här metoden för testning om du inte är beredd att fungera som din egen certifikatutfärdare.
 

## <a name="roll-the-certificate-on-the-device"></a>Rulla certifikatet på enheten

Certifikat på en enhet bör alltid lagras på ett säkert ställe som en [maskinvarusäkerhetsmodul (HSM).](concepts-device.md#hardware-security-module) Hur du rullar enhetscertifikat beror på hur de skapades och installerades i enheterna i första hand. 

Om du har fått dina certifikat från en tredje part måste du undersöka hur de rullar sina certifikat. Processen kan ingå i ditt arrangemang med dem, eller det kan vara en separat tjänst de erbjuder. 

Om du hanterar dina egna enhetscertifikat måste du skapa en egen pipeline för att uppdatera certifikat. Kontrollera att både gamla och nya lövcertifikat har samma gemensamma namn (CN). Genom att ha samma CN kan enheten återetablera sig själv utan att skapa en dubblettregistreringspost. 


## <a name="roll-the-certificate-in-the-iot-hub"></a>Rulla certifikatet i IoT-hubben

Enhetscertifikatet kan läggas till manuellt i en IoT-hubb. Certifikatet kan också automatiseras med hjälp av en tjänstinstans för enhetsetablering. I den här artikeln antar vi att en tjänstinstans för enhetsetablering används för automatisk etablering.

När en enhet ursprungligen etableras genom automatisk etablering startar den och kontaktar etableringstjänsten. Etableringstjänsten svarar genom att utföra en identitetskontroll innan du skapar en enhetsidentitet i en IoT-hubb med enhetens lövcertifikat som autentiseringsuppgifter. Etableringstjänsten talar sedan om för enheten vilken IoT-hubb den har tilldelats och enheten använder sedan sitt lövcertifikat för att autentisera och ansluta till IoT-hubben. 

När ett nytt lövcertifikat har rullats till enheten kan det inte längre ansluta till IoT-hubben eftersom det använder ett nytt certifikat för att ansluta. IoT-hubben känner bara igen enheten med det gamla certifikatet. Resultatet av enhetens anslutningsförsök blir ett "obehörigt" anslutningsfel. För att lösa det här felet måste du uppdatera registreringsposten för enheten för att ta hänsyn till enhetens nya lövcertifikat. Sedan kan etableringstjänsten uppdatera registerinformationen för IoT Hub-enheten efter behov när enheten återetableras. 

Ett möjligt undantag från det här anslutningsfelet är ett scenario där du har skapat en [registreringsgrupp](concepts-service.md#enrollment-group) för enheten i etableringstjänsten. I det här fallet, om du inte rullar rot- eller mellanliggande certifikat i enhetens certifikatkedja, identifieras enheten om det nya certifikatet ingår i förtroendekedjan som definierats i registreringsgruppen. Om det här scenariot uppstår som en reaktion på en säkerhetsöverträdelse bör du åtminstone svartlista de specifika enhetscertifikaten i gruppen som anses vara överträdda. Mer information finns [i Svartlista specifika enheter i en registreringsgrupp](https://docs.microsoft.com/azure/iot-dps/how-to-revoke-device-access-portal#blacklist-specific-devices-in-an-enrollment-group).

Det sker att uppdatera registreringsposter för bevalsade certifikat på sidan **Hantera registreringar.** Så här öppnar du den sidan:

1. Logga in på [Azure-portalen](https://portal.azure.com) och navigera till IoT Hub Device Provisioning Service-instansen som har registreringsposten för din enhet.

2. Klicka på **Hantera registreringar**.

    ![Hantera registreringar](./media/how-to-roll-certificates/manage-enrollments-portal.png)


Hur du hanterar uppdateringen av registreringsposten beror på om du använder enskilda registreringar eller gruppregistreringar. De rekommenderade procedurerna varierar också beroende på om du rullande certifikat på grund av ett säkerhetsbrott eller certifikatets förfallodatum. I följande avsnitt beskrivs hur du hanterar dessa uppdateringar.


## <a name="individual-enrollments-and-security-breaches"></a>Enskilda registreringar och säkerhetsöverträdelser

Om du rullande certifikat som svar på en säkerhetsöverträdelse bör du använda följande metod som tar bort det aktuella certifikatet omedelbart:

1. Klicka på **Enskilda registreringar**och klicka på registrerings-ID-posten i listan. 

2. Klicka på knappen **Ta bort aktuellt certifikat** och klicka sedan på mappikonen för att välja det nya certifikat som ska överföras för registreringsposten. Klicka på **Spara** när du är klar.

    Dessa steg bör slutföras för det primära och sekundära certifikatet, om båda är komprometterade.

    ![Hantera enskilda registreringar](./media/how-to-roll-certificates/manage-individual-enrollments-portal.png)

3. När det komprometterade certifikatet har tagits bort från etableringstjänsten kan certifikatet fortfarande användas för att göra enhetsanslutningar till IoT-hubben så länge det finns en enhetsregistrering för den. Du kan ta itu med detta på två sätt: 

    Det första sättet är att manuellt navigera till din IoT-hubb och omedelbart ta bort enhetsregistreringen som är associerad med det komprometterade certifikatet. När enheten sedan etablerar igen med ett uppdaterat certifikat skapas en ny enhetsregistrering.     

    ![Ta bort registrering av IoT-hubbenheter](./media/how-to-roll-certificates/remove-hub-device-registration.png)

    Det andra sättet skulle vara att använda ometablering stöd för att återetablera enheten till samma IoT-hubb. Den här metoden kan användas för att ersätta certifikatet för enhetsregistreringen på IoT-hubben. Mer information finns i [Så här återetablera enheter](how-to-reprovision.md).

## <a name="individual-enrollments-and-certificate-expiration"></a>Enskilda registreringar och certifikatets förfallodatum

Om du rullande certifikat för att hantera certifikatets förfallodatum bör du använda den sekundära certifikatkonfigurationen på följande sätt för att minska stilleståndstiden för enheter som försöker etablera.

Senare när det sekundära certifikatet också närmar sig förfallodatum och måste rullas kan du rotera till att använda den primära konfigurationen. Om du roterar mellan de primära och sekundära certifikaten på det här sättet minskar stilleståndstiden för enheter som försöker etableras.


1. Klicka på **Enskilda registreringar**och klicka på registrerings-ID-posten i listan. 

2. Klicka på **Sekundärt certifikat** och sedan på mappikonen för att välja det nya certifikat som ska överföras för registreringsposten. Klicka på **Spara**.

    ![Hantera enskilda registreringar med det sekundära certifikatet](./media/how-to-roll-certificates/manage-individual-enrollments-secondary-portal.png)

3. Senare när det primära certifikatet har upphört att gälla kommer du tillbaka och tar bort det primära certifikatet genom att klicka på knappen **Ta bort aktuellt certifikat.**

## <a name="enrollment-groups-and-security-breaches"></a>Registreringsgrupper och säkerhetsöverträdelser

Om du vill uppdatera en gruppregistrering som svar på en säkerhetsöverträdelse bör du använda någon av följande metoder som tar bort den aktuella rotcertifikatutfärdaren eller mellanliggande certifikat omedelbart.

#### <a name="update-compromised-root-ca-certificates"></a>Uppdatera komprometterade rotcertifikatutfärdare

1. Klicka på fliken Certifikat för tjänstinstansen För **enhetsetablering.**

2. Klicka på det komprometterade certifikatet i listan och klicka sedan på knappen **Ta bort.** Bekräfta borttagningen genom att ange certifikatnamnet och klicka på **OK**. Upprepa den här processen för alla komprometterade certifikat.

    ![Ta bort rotcertifikatutfärdarcertifikat](./media/how-to-roll-certificates/delete-root-cert.png)

3. Följ stegen som beskrivs i [Konfigurera verifierade certifikatutfärdare](how-to-verify-certificates.md) för att lägga till och verifiera nya rotcertifikatutfärdare.

4. Klicka på fliken **Hantera registreringar** för tjänstinstansen För enhetsetablering och klicka på listan **Registreringsgrupper.** Klicka på registreringsgruppsnamnet i listan.

5. Klicka på **CERTIFIKATUTFÄRDERcertifikat**och välj det nya rotcertifikatutfärdarcertifikatet. Klicka sedan på **Spara**. 

    ![Välj det nya rotcertifikatutfärdarcertifikatet](./media/how-to-roll-certificates/select-new-root-cert.png)

6. När det komprometterade certifikatet har tagits bort från etableringstjänsten kan certifikatet fortfarande användas för att göra enhetsanslutningar till IoT-hubben så länge enhetsregistreringar för det finns där. Du kan ta itu med detta på två sätt: 

    Det första sättet är att manuellt navigera till din IoT-hubb och omedelbart ta bort enhetsregistreringen som är associerad med det komprometterade certifikatet. När dina enheter etableras igen med uppdaterade certifikat skapas en ny enhetsregistrering för var och en.     

    ![Ta bort registrering av IoT-hubbenheter](./media/how-to-roll-certificates/remove-hub-device-registration.png)

    Det andra sättet skulle vara att använda ometablering stöd för att återetablera dina enheter till samma IoT-hubb. Den här metoden kan användas för att ersätta certifikat för enhetsregistreringar på IoT-hubben. Mer information finns i [Så här återetablera enheter](how-to-reprovision.md).



#### <a name="update-compromised-intermediate-certificates"></a>Uppdatera komprometterade mellanliggande certifikat

1. Klicka på **Registreringsgrupper**och sedan på gruppnamnet i listan. 

2. Klicka på **Mellanliggande certifikat**och **Ta bort aktuellt certifikat**. Klicka på mappikonen om du vill navigera till det nya mellanliggande certifikat som ska överföras för registreringsgruppen. Klicka på **Spara** när du är klar. Dessa steg bör slutföras för både det primära och sekundära certifikatet, om båda är komprometterade.

    Det här nya mellanliggande certifikatet bör signeras av ett verifierat rotcertifikatutfärdarcertifikat som redan har lagts till i etableringstjänsten. Mer information finns i [X.509-certifikat](concepts-security.md#x509-certificates).

    ![Hantera enskilda registreringar](./media/how-to-roll-certificates/enrollment-group-delete-intermediate-cert.png)


3. När det komprometterade certifikatet har tagits bort från etableringstjänsten kan certifikatet fortfarande användas för att göra enhetsanslutningar till IoT-hubben så länge enhetsregistreringar för det finns där. Du kan ta itu med detta på två sätt: 

    Det första sättet är att manuellt navigera till din IoT-hubb och omedelbart ta bort enhetsregistreringen som är associerad med det komprometterade certifikatet. När dina enheter etableras igen med uppdaterade certifikat skapas en ny enhetsregistrering för var och en.     

    ![Ta bort registrering av IoT-hubbenheter](./media/how-to-roll-certificates/remove-hub-device-registration.png)

    Det andra sättet skulle vara att använda ometablering stöd för att återetablera dina enheter till samma IoT-hubb. Den här metoden kan användas för att ersätta certifikat för enhetsregistreringar på IoT-hubben. Mer information finns i [Så här återetablera enheter](how-to-reprovision.md).


## <a name="enrollment-groups-and-certificate-expiration"></a>Registreringsgrupper och certifikatets förfallodatum

Om du rullande certifikat för att hantera certifikatets förfallodatum bör du använda den sekundära certifikatkonfigurationen på följande sätt för att säkerställa att det inte finns några driftstopp för enheter som försöker etablera.

Senare när det sekundära certifikatet också närmar sig förfallodatum och måste rullas kan du rotera till att använda den primära konfigurationen. Om du roterar mellan de primära och sekundära certifikaten på detta sätt säkerställs inga driftstopp för enheter som försöker etableras. 

#### <a name="update-expiring-root-ca-certificates"></a>Uppdatera utgående rotcertifikatutfärdare

1. Följ stegen som beskrivs i [Konfigurera verifierade certifikatutfärdare](how-to-verify-certificates.md) för att lägga till och verifiera nya rotcertifikatutfärdare.

2. Klicka på fliken **Hantera registreringar** för tjänstinstansen För enhetsetablering och klicka på listan **Registreringsgrupper.** Klicka på registreringsgruppsnamnet i listan.

3. Klicka på **CERTIFIKATUTFÄRDERcertifikat**och välj det nya rotcertifikatutfärdarcertifikatet under konfigurationen **för sekundärt certifikat.** Klicka sedan på **Spara**. 

    ![Välj det nya rotcertifikatutfärdarcertifikatet](./media/how-to-roll-certificates/select-new-root-secondary-cert.png)

4. Senare när det primära certifikatet har upphört att gälla klickar du på fliken **Certifikat** för tjänstinstansen För enhetsetablering. Klicka på det utgångna certifikatet i listan och klicka sedan på knappen **Ta bort.** Bekräfta borttagningen genom att ange certifikatnamnet och klicka på **OK**.

    ![Ta bort rotcertifikatutfärdarcertifikat](./media/how-to-roll-certificates/delete-root-cert.png)



#### <a name="update-expiring-intermediate-certificates"></a>Uppdatera utgående mellanliggande certifikat


1. Klicka på **Registreringsgrupper**och klicka på gruppnamnet i listan. 

2. Klicka på **Sekundärt certifikat** och sedan på mappikonen för att välja det nya certifikat som ska överföras för registreringsposten. Klicka på **Spara**.

    Det här nya mellanliggande certifikatet bör signeras av ett verifierat rotcertifikatutfärdarcertifikat som redan har lagts till i etableringstjänsten. Mer information finns i [X.509-certifikat](concepts-security.md#x509-certificates).

   ![Hantera enskilda registreringar med det sekundära certifikatet](./media/how-to-roll-certificates/manage-enrollment-group-secondary-portal.png)

3. Senare när det primära certifikatet har upphört att gälla kommer du tillbaka och tar bort det primära certifikatet genom att klicka på knappen **Ta bort aktuellt certifikat.**


## <a name="reprovision-the-device"></a>Återetablera enheten

När certifikatet har rullats på både enheten och enhetsetableringstjänsten kan enheten återetablera sig själv genom att kontakta tjänsten Enhetsetablering. 

Ett enkelt sätt att programmera enheter för att återetablera är att programmera enheten att kontakta etableringstjänsten för att gå igenom etableringsflödet om enheten får ett "obehörigt" fel från att försöka ansluta till IoT-hubben.

Ett annat sätt är att både de gamla och de nya certifikaten ska vara giltiga för en kort överlappning och använda IoT-hubben för att skicka ett kommando till enheter för att få dem att registrera om via etableringstjänsten för att uppdatera sin IoT Hub-anslutningsinformation. Eftersom varje enhet kan bearbeta kommandon på olika sätt måste du programmera enheten för att veta vad du ska göra när kommandot anropas. Det finns flera sätt att styra din enhet via IoT Hub, och vi rekommenderar att du använder [direkta metoder](../iot-hub/iot-hub-devguide-direct-methods.md) eller [jobb](../iot-hub/iot-hub-devguide-jobs.md) för att initiera processen.

När ometablering är klar kan enheter ansluta till IoT Hub med sina nya certifikat.


## <a name="blacklist-certificates"></a>Svartlista certifikat

Som svar på en säkerhetsöverträdelse kan du behöva svartlista ett enhetscertifikat. Om du vill svartlista ett enhetscertifikat inaktiverar du registreringsposten för målenheten/certifikatet. Mer information finns i svartlistningsenheter i artikeln [Hantera avregistrering.](how-to-revoke-device-access-portal.md)

När ett certifikat har inkluderats som en del av en inaktiverad registreringspost misslyckas alla försök att registrera sig med en IoT-hubb med certifikat även om det är aktiverat som en del av en annan registreringspost.
 



## <a name="next-steps"></a>Nästa steg

- Mer information om X.509-certifikat i enhetsetableringstjänsten [finns](concepts-security.md) i 
- Mer information om hur du gör proof-of-possession för X.509 CA-certifikat med Azure IoT Hub Device Provisioning Service, se [Så här verifierar du certifikat](how-to-verify-certificates.md)
- Mer information om hur du använder portalen för att skapa en registreringsgrupp finns i [Hantera enhetsregistreringar med Azure Portal](how-to-manage-enrollments.md).










