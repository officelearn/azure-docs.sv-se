---
title: Felsöka problem med datahanteringsgateway
description: Innehåller tips om felsökning av problem relaterade till Data Management Gateway.
services: data-factory
author: nabhishek
manager: anandsub
ms.assetid: c6756c37-4e5a-4d1e-ab52-365f149b4128
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/01/2017
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 09d51de3ae0bd4baca585d2abdd936b1a29567d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80065035"
---
# <a name="troubleshoot-issues-with-using-data-management-gateway"></a>Felsöka problem med gateway för datahantering
Den här artikeln innehåller information om felsökningsproblem med datahanteringsgateway.

> [!NOTE]
> Den här artikeln gäller för version 1 av Azure Data Factory. Om du använder den aktuella versionen av datafabrikstjänsten läser du [självvärderade integrationskörning i Data Factory](../create-self-hosted-integration-runtime.md).

Mer information om gatewayen finns i artikeln [Data Management Gateway.](data-factory-data-management-gateway.md) Se artikeln Flytta data mellan lokala och moln för en genomgång av att flytta data från en lokal SQL Server-databas till Microsoft Azure [Blob-lagring](data-factory-move-data-between-onprem-and-cloud.md) med hjälp av gatewayen.

## <a name="failed-to-install-or-register-gateway"></a>Det gick inte att installera eller registrera gatewayen
### <a name="1-problem"></a>1. Problem
Det här felmeddelandet visas när du installerar och registrerar en gateway, specifikt när du hämtar installationsfilen för gatewayen.

`Unable to connect to the remote server". Please check your local settings (Error Code: 10003).`

#### <a name="cause"></a>Orsak
Datorn som du försöker installera gatewayen på har inte lyckats hämta den senaste installationsfilen för gatewayen från hämtningscentralen på grund av ett nätverksproblem.

#### <a name="resolution"></a>Lösning
Kontrollera inställningarna för brandväggens proxyserver för att se om inställningarna blockerar nätverksanslutningen från datorn till [hämtningscentralen](https://download.microsoft.com/)och uppdaterar inställningarna därefter.

Alternativt kan du hämta installationsfilen för den senaste gatewayen från [nedladdningscentret](https://www.microsoft.com/download/details.aspx?id=39717) på andra datorer som kan komma åt nedladdningscentret. Du kan sedan kopiera installationsfilen till värddatorn för gatewayen och köra den manuellt för att installera och uppdatera gatewayen.

### <a name="2-problem"></a>2. Problem
Det här felet visas när du försöker installera en gateway genom att klicka **på installera direkt på** den här datorn i Azure-portalen.

`Error:  Abort installing a new gateway on this computer because this computer has an existing installed gateway and a computer without any installed gateway is required for installing a new gateway.`  

#### <a name="cause"></a>Orsak
En gateway är redan installerad på datorn.

#### <a name="resolution"></a>Lösning
Avinstallera den befintliga gatewayen på datorn och klicka på **installera direkt på den här datorn** länken igen.

### <a name="3-problem"></a>3. Problem
Det här felet kan visas när du registrerar en ny gateway.

`Error: The gateway has encountered an error during registration.`

#### <a name="cause"></a>Orsak
Det här meddelandet kan visas av något av följande skäl:

* Gateway-tangentens format är ogiltigt.
* Gateway-nyckeln har ogiltigförklarats.
* Gateway-nyckeln har återskapats från portalen.  

#### <a name="resolution"></a>Lösning
Kontrollera om du använder rätt gatewaynyckel från portalen. Om det behövs återskapar du en nyckel och använder nyckeln för att registrera gatewayen.

### <a name="4-problem"></a>4. Problem
Följande felmeddelande kan visas när du registrerar en gateway.

`Error: The content or format of the gateway key "{gatewayKey}" is invalid, please go to azure portal to create one new gateway or regenerate the gateway key.`



![Innehållet eller formatet på nyckeln är ogiltigt](media/data-factory-troubleshoot-gateway-issues/invalid-format-gateway-key.png)

#### <a name="cause"></a>Orsak
Innehållet eller formatet för indatagatewaynyckeln är felaktigt. En av anledningarna kan vara att du bara kopierade en del av nyckeln från portalen eller att du använder en ogiltig nyckel.

#### <a name="resolution"></a>Lösning
Generera en gateway-nyckel i portalen och använd kopieringsknappen för att kopiera hela nyckeln. Klistra sedan in den i det här fönstret för att registrera gatewayen.

### <a name="5-problem"></a>5. Problem
Följande felmeddelande kan visas när du registrerar en gateway.

`Error: The gateway key is invalid or empty. Specify a valid gateway key from the portal.`

![Gateway-nyckeln är ogiltig eller tom](media/data-factory-troubleshoot-gateway-issues/gateway-key-is-invalid-or-empty.png)

#### <a name="cause"></a>Orsak
Gateway-nyckeln har återskapats eller gatewayen har tagits bort i Azure-portalen. Det kan också hända om inställningarna för Data Management Gateway inte är senaste.

#### <a name="resolution"></a>Lösning
Kontrollera om inställningarna för Data Management Gateway är den senaste versionen hittar du den senaste versionen på Microsoft [Download Center](https://go.microsoft.com/fwlink/p/?LinkId=271260).

Om installationen är aktuell/ senaste och gateway fortfarande finns på Portal återskapar du gatewaynyckeln i Azure-portalen och använder kopieringsknappen för att kopiera hela nyckeln och klistra sedan in den i det här fönstret för att registrera gatewayen. Annars återskapar du gatewayen och börjar om.

### <a name="6-problem"></a>6. Problem
Följande felmeddelande kan visas när du registrerar en gateway.

`Error: Gateway has been online for a while, then shows “Gateway is not registered” with the status “Gateway key is invalid”`

![Gateway-nyckeln är ogiltig eller tom](media/data-factory-troubleshoot-gateway-issues/gateway-not-registered-key-invalid.png)

#### <a name="cause"></a>Orsak
Det här felet kan bero på att gatewayen har tagits bort eller så har den associerade gatewaynyckeln återskapats.

#### <a name="resolution"></a>Lösning
Om gatewayen har tagits bort återskapar du gatewayen från portalen, klickar på **Registrera, kopierar**nyckeln från portalen, klistrar in den och försöker registrera gatewayen.

Om gatewayen fortfarande finns men dess nyckel har återskapats använder du den nya nyckeln för att registrera gatewayen. Om du inte har nyckeln återskapar du nyckeln igen från portalen.

### <a name="7-problem"></a>7. Problem
När du registrerar en gateway kan du behöva ange sökväg och lösenord för ett certifikat.

![Ange certifikat](media/data-factory-troubleshoot-gateway-issues/specify-certificate.png)

#### <a name="cause"></a>Orsak
Gatewayen har registrerats på andra datorer tidigare. Under den första registreringen av en gateway har ett krypteringscertifikat associerats med gatewayen. Certifikatet kan antingen genereras själv av gatewayen eller tillhandahållas av användaren.  Det här certifikatet används för att kryptera autentiseringsuppgifter för datalagret (länkad tjänst).  

![Exportera certifikatet](media/data-factory-troubleshoot-gateway-issues/export-certificate.png)

När gatewayen återställs på en annan värddator ber registreringsguiden certifikatet om det här certifikatet att dekryptera autentiseringsuppgifter som tidigare krypterats med det här certifikatet.  Utan det här certifikatet kan autentiseringsuppgifterna inte dekrypteras av den nya gatewayen och efterföljande kopieringsaktivitetskörningar som är associerade med den nya gatewayen misslyckas.  

#### <a name="resolution"></a>Lösning
Om du har exporterat certifikatet för autentiseringsuppgifter från den ursprungliga gateway-datorn med hjälp av knappen **Exportera** på fliken **Inställningar** i Configuration Manager för datahanteringsgateway använder du certifikatet här.

Du kan inte hoppa över det här steget när du återställer en gateway. Om certifikatet saknas måste du ta bort gatewayen från portalen och återskapa en ny gateway.  Uppdatera dessutom alla länkade tjänster som är relaterade till gatewayen genom att ange deras autentiseringsuppgifter igen.

### <a name="8-problem"></a>8. Problem
Följande felmeddelande kan visas.

`Error: The remote server returned an error: (407) Proxy Authentication Required.`

#### <a name="cause"></a>Orsak
Det här felet inträffar när gatewayen finns i en miljö som kräver en HTTP-proxy för att komma åt Internet-resurser, eller proxyns autentiseringslösenord ändras men det uppdateras inte i enlighet med detta i gatewayen.

#### <a name="resolution"></a>Lösning
Följ anvisningarna i avsnittet Proxyserveröverväganden i den här artikeln och konfigurera proxyinställningar med Configuration Manager för Data Management Gateway.

## <a name="gateway-is-online-with-limited-functionality"></a>Gateway är online med begränsad funktionalitet
### <a name="1-problem"></a>1. Problem
Du ser status för gatewayen som online med begränsad funktionalitet.

#### <a name="cause"></a>Orsak
Du ser statusen för gatewayen som online med begränsad funktionalitet av något av följande skäl:

* Gatewayen kan inte ansluta till molntjänsten via Azure Service Bus.
* Molntjänsten kan inte ansluta till gateway via Service Bus.

När gatewayen är online med begränsad funktionalitet kanske du inte kan använda guiden Data Factory Copy för att skapa datapipelpipel för kopiering av data till eller från lokala datalager. Som en lösning kan du använda Data Factory Editor i portalen, Visual Studio eller Azure PowerShell.

#### <a name="resolution"></a>Lösning
Lösningen på det här problemet (online med begränsad funktionalitet) baseras på om gatewayen inte kan ansluta till molntjänsten eller åt andra hållet. Följande avsnitt innehåller dessa lösningar.

### <a name="2-problem"></a>2. Problem
Du ser följande fel.

`Error: Gateway cannot connect to cloud service through service bus`

![Gatewayen kan inte ansluta till molntjänsten](media/data-factory-troubleshoot-gateway-issues/gateway-cannot-connect-to-cloud-service.png)

#### <a name="cause"></a>Orsak
Gatewayen kan inte ansluta till molntjänsten via Service Bus.

#### <a name="resolution"></a>Lösning
Följ dessa steg för att ansluta gatewayen igen:

1. Tillåt utgående IP-adressregler på gateway-datorn och företagets brandvägg. Du kan hitta IP-adresser från Windows Event Log (ID == 401): Ett försök gjordes att komma åt en socket på ett sätt som är förbjudet av dess åtkomstbehörigheter XX. Xx. Xx. XX:9350.
1. Konfigurera proxyinställningar på gatewayen. Mer information finns i avsnittet Om hur du överväger proxyservern.
1. Aktivera utgående portar 5671 och 9350-9354 på både Windows-brandväggen på gateway-datorn och företagets brandvägg. Mer information finns i avsnittet Portar och brandväggar. Det här steget är valfritt, men vi rekommenderar det för prestandaövervägande.

### <a name="3-problem"></a>3. Problem
Du ser följande fel.

`Error: Cloud service cannot connect to gateway through service bus.`

#### <a name="cause"></a>Orsak
Ett tillfälligt fel i nätverksanslutningen.

#### <a name="resolution"></a>Lösning
Följ dessa steg för att ansluta gatewayen igen:

1. Vänta ett par minuter, anslutningen återställs automatiskt när felet är borta.
1. Om felet kvarstår startar du om gateway-tjänsten.

## <a name="failed-to-author-linked-service"></a>Det gick inte att skapa länkad tjänst
### <a name="problem"></a>Problem
Det här felet kan visas när du försöker använda Autentiseringshanteraren i portalen för att ange autentiseringsuppgifter för en ny länkad tjänst eller uppdatera autentiseringsuppgifter för en befintlig länkad tjänst.

`Error: The data store '<Server>/<Database>' cannot be reached. Check connection settings for the data source.`

När det här felet visas kan inställningssidan för Konfigurationshanteraren för Data Management Gateway se ut så här.

![Det går inte att nå databasen](media/data-factory-troubleshoot-gateway-issues/database-cannot-be-reached.png)

#### <a name="cause"></a>Orsak
TLS/SSL-certifikatet kan ha gått förlorat på gateway-datorn. Gateway-datorn kan inte läsa in certifikatet som används för TLS-kryptering. Du kan också se ett felmeddelande i händelseloggen som liknar följande meddelande.

 `Unable to get the gateway settings from cloud service. Check the gateway key and the network connection. (Certificate with thumbprint cannot be loaded.)`

#### <a name="resolution"></a>Lösning
Så här löser du problemet:

1. Starta Konfigurationshanteraren för datahanteringsgateway.
2. Växla till fliken **Inställningar.**  
3. Klicka på knappen **Ändra** om du vill ändra TLS/SSL-certifikatet.

   ![Knappen Ändra certifikat](media/data-factory-troubleshoot-gateway-issues/change-button-ssl-certificate.png)
4. Välj ett nytt certifikat som TLS/SSL-certifikat. Du kan använda alla TLS/SSL-certifikat som genereras av dig eller någon organisation.

   ![Ange certifikat](media/data-factory-troubleshoot-gateway-issues/specify-http-end-point.png)

## <a name="copy-activity-fails"></a>Kopieringsaktiviteten misslyckas
### <a name="problem"></a>Problem
Du kanske märker följande fel på "UserErrorFailedToConnectToSqlserver" när du har konfigurerat en pipeline i portalen.

`Error: Copy activity encountered a user error: ErrorCode=UserErrorFailedToConnectToSqlServer,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Cannot connect to SQL Server`

#### <a name="cause"></a>Orsak
Detta kan inträffa av olika skäl, och begränsning varierar i enlighet med detta.

#### <a name="resolution"></a>Lösning
Tillåt utgående TCP-anslutningar via tcp-port/1433 på datahanteringsgateway-klientsidan innan du ansluter till en SQL-databas.

Om måldatabasen är en Azure SQL-databas kontrollerar du även SQL Server-brandväggsinställningarna för Azure.

I följande avsnitt finns en information om hur du testar anslutningen till det lokala datalagret.

## <a name="data-store-connection-or-driver-related-errors"></a>Datalageranslutning eller drivrutinsrelaterade fel
Om du ser anslutningsfel för datalager eller drivrutinsrelaterade fel gör du följande:

1. Starta Konfigurationshanteraren för datahantering gateway på gateway-datorn.
2. Växla till fliken **Diagnostik.**
3. Lägg till gatewaygruppsvärdena i **Testanslutning.**
4. Klicka på **Testa** om du kan ansluta till den lokala datakällan från gateway-datorn med hjälp av anslutningsinformation och autentiseringsuppgifter. Om testanslutningen fortfarande misslyckas när du har installerat en drivrutin, ska du starta om gatewayen så att den använder den senaste ändringen.

![Fliken Testa anslutning i diagnostik](media/data-factory-troubleshoot-gateway-issues/test-connection-in-diagnostics-tab.png)

## <a name="gateway-logs"></a>Gateway-loggar
### <a name="send-gateway-logs-to-microsoft"></a>Skicka gatewayloggar till Microsoft
När du kontaktar Microsoft Support för att få hjälp med felsökning av gatewayproblem kan du bli ombedd att dela dina gatewayloggar. Med lanseringen av gatewayen kan du dela nödvändiga gatewayloggar med två knappklick i Configuration Manager för Data Management Gateway.    

1. Växla till fliken Diagnostik i **Konfigurationshanteraren** för datahanteringsgateway.

    ![Fliken Diagnostik i Data Management Gateway](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-diagnostics-tab.png)
2. Klicka på **Skicka loggar** om du vill visa följande dialogruta.

    ![Skicka loggar för datahanteringsgateway](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-dialog.png)
3. (Valfritt) Klicka på **Visa loggar** för att granska loggar i loggboken.
4. (Valfritt) Klicka på **sekretessen** om du vill läsa sekretesspolicyn för Microsofts webbtjänster.
5. När du är nöjd med vad du ska ladda upp klickar du på **Skicka loggar** för att faktiskt skicka loggarna från de senaste sju dagarna till Microsoft för felsökning. Du bör se status för skicka-loggar operation som visas i följande skärmdump.

    ![Skicka loggstatus för datahanteringsgateway](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-status.png)
6. När åtgärden är klar visas en dialogruta som visas i följande skärmbild.

    ![Skicka loggstatus för datahanteringsgateway](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-result.png)
7. Spara **rapport-ID:t** och dela det med Microsoft Support. Rapport-ID används för att hitta gatewayloggarna som du har laddat upp för felsökning.  Rapport-ID:et sparas också i loggboken.  Du kan hitta den genom att titta på händelse-ID "25", och kontrollera datum och tid.

    ![Rapport-ID för skicka loggar för datahanteringsgateway](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-report-id.png)    

### <a name="archive-gateway-logs-on-gateway-host-machine"></a>Arkivera gatewayloggar på gateway-värddator
Det finns vissa scenarier där du har gatewayproblem och du kan inte dela gatewayloggar direkt:

* Du installerar gatewayen manuellt och registrerar gatewayen.
* Du försöker registrera gatewayen med en återskapad nyckel i Configuration Manager för datahanteringsgateway.
* Du försöker skicka loggar och tjänsten gateway-värd kan inte anslutas.

I de här scenarierna kan du spara gatewayloggar som en zip-fil och dela den när du kontaktar Microsoft-supporten. Om du till exempel får ett felmeddelande medan du registrerar gatewayen som visas i följande skärmbild.   

![Registreringsfel för datahanteringsgateway](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-registration-error.png)

Klicka på länken **Arkiv gateway loggar** för att arkivera och spara loggar och sedan dela zip-filen med Microsoft-stöd.

![Arkivera loggar för datahanteringsgateway](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-archive-logs.png)

### <a name="locate-gateway-logs"></a>Hitta gatewayloggar
Du hittar detaljerad gateway-logginformation i Windows-händelseloggarna.

1. Starta Windows **Loggboken**.
2. Leta upp loggar i mappen Program och tjänster loggar**datahanteringsgateway.** **Application and Services Logs** > 

   När du felsöker gatewayrelaterade problem letar du efter felnivåhändelser i loggboken.

![Data Management Gateway loggar i loggboken](media/data-factory-troubleshoot-gateway-issues/gateway-logs-event-viewer.png)
