---
title: Felsökning av problem med Data Management Gateway | Microsoft Docs
description: Innehåller tips för att felsöka problem som rör Data Management Gateway.
services: data-factory
author: nabhishek
manager: craigg
ms.assetid: c6756c37-4e5a-4d1e-ab52-365f149b4128
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/01/2017
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 66e6725dd4e23eeaf0c8d0dcff6e5d26e3743218
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34624292"
---
# <a name="troubleshoot-issues-with-using-data-management-gateway"></a>Felsöka problem med gateway för datahantering
Den här artikeln innehåller information om felsökning av problem med att använda Data Management Gateway.

> [!NOTE]
> Den här artikeln gäller för version 1 av Azure Data Factory, som är allmänt tillgänglig (GA). Om du använder version 2 av Data Factory-tjänsten, som finns i förhandsgranskningen, se [egenvärdbaserat integrering körning i Data Factory version 2](../create-self-hosted-integration-runtime.md).

Finns det [Data Management Gateway](data-factory-data-management-gateway.md) artikel detaljerad information om gatewayen. Finns det [flytta data mellan lokalt och i molnet](data-factory-move-data-between-onprem-and-cloud.md) artikeln en genomgång för att flytta data från en lokal SQL Server-databas till Microsoft Azure Blob storage med hjälp av gatewayen.

## <a name="failed-to-install-or-register-gateway"></a>Det gick inte att installera eller registrera gateway
### <a name="1-problem"></a>1. Problem
Du ser det här felmeddelandet när du installerar och registrerar en gateway, särskilt när laddades ned installationsfilen för gateway.

`Unable to connect to the remote server". Please check your local settings (Error Code: 10003).`

#### <a name="cause"></a>Orsak
Datorn som du försöker installera gatewayen kunde inte hämta den senaste gateway-filen för installation från download center på grund av ett nätverksproblem.

#### <a name="resolution"></a>Lösning
Kontrollera brandväggen proxy-serverinställningarna för att se om inställningarna blockera nätverksanslutningen från datorn till den [hämtningssidan](https://download.microsoft.com/), och uppdatera inställningarna för detta.

Alternativt kan du hämta installationsfilerna för den senaste gatewayen från den [hämtningssidan](https://www.microsoft.com/download/details.aspx?id=39717) på andra datorer som har åtkomst till download center. Du kan kopiera installationsfilen till värddatorn gateway och kör den manuellt för att installera och uppdatera gatewayen.

### <a name="2-problem"></a>2. Problem
Det här felet visas när du försöker installera en gateway genom att klicka på **installera direkt på den här datorn** i Azure-portalen.

`Error:  Abort installing a new gateway on this computer because this computer has an existing installed gateway and a computer without any installed gateway is required for installing a new gateway.`  

#### <a name="cause"></a>Orsak
En gateway är redan installerad på datorn.

#### <a name="resolution"></a>Lösning
Avinstallera den befintliga gatewayen på datorn och klicka på den **installera direkt på den här datorn** igen.

### <a name="3-problem"></a>3. Problem
Det här felet kan uppstå när du registrerar en ny gateway.

`Error: The gateway has encountered an error during registration.`

#### <a name="cause"></a>Orsak
Du kan se det här meddelandet av något av följande skäl:

* Formatet för gatewaynyckeln är ogiltig.
* Gatewaynyckeln har ogiltigförklarats.
* Gatewaynyckeln har varit återskapas från portalen.  

#### <a name="resolution"></a>Lösning
Kontrollera om du använder rätt gateway-nyckeln från portalen. Om det behövs kan återskapa en nyckel och använder för att registrera gatewayen.

### <a name="4-problem"></a>4. Problem
Du kan se följande felmeddelande när du registrera en gateway.

`Error: The content or format of the gateway key "{gatewayKey}" is invalid, please go to azure portal to create one new gateway or regenerate the gateway key.`



![Innehåll eller format för nyckel är ogiltig](media/data-factory-troubleshoot-gateway-issues/invalid-format-gateway-key.png)

#### <a name="cause"></a>Orsak
Innehåll eller format för inkommande gatewaynyckeln är felaktigt. En av orsaker kan vara att du har kopierat endast en del av nyckeln från portalen eller du använder en ogiltig nyckel.

#### <a name="resolution"></a>Lösning
Skapa en gatewaynyckel i portalen och Använd kopieringsknappen för att kopiera hela nyckeln. Klistra in den i det här fönstret för att registrera gatewayen.

### <a name="5-problem"></a>5. Problem
Du kan se följande felmeddelande när du registrera en gateway.

`Error: The gateway key is invalid or empty. Specify a valid gateway key from the portal.`

![Gatewaynyckeln är ogiltig eller tom](media/data-factory-troubleshoot-gateway-issues/gateway-key-is-invalid-or-empty.png)

#### <a name="cause"></a>Orsak
Gatewaynyckeln har varit återskapas eller gatewayen har tagits bort i Azure-portalen. Det kan också inträffa om Data Management Gateway-installationen inte är senaste.

#### <a name="resolution"></a>Lösning
Kontrollera om Data Management Gateway-installationen är den senaste versionen kan du hittar den senaste versionen på Microsoft [hämtningssidan](https://go.microsoft.com/fwlink/p/?LinkId=271260).

Om installationen är aktuella / senaste och gateway finns fortfarande på portalen, återskapa gatewaynyckeln i Azure-portalen och Använd kopieringsknappen för att kopiera hela nyckeln och klistra in den i det här fönstret och registrera gatewayen. Annars återskapa gatewayen och börja om från början.

### <a name="6-problem"></a>6. Problem
Du kan se följande felmeddelande när du registrera en gateway.

`Error: Gateway has been online for a while, then shows “Gateway is not registered” with the status “Gateway key is invalid”`

![Gatewaynyckeln är ogiltig eller tom](media/data-factory-troubleshoot-gateway-issues/gateway-not-registered-key-invalid.png)

#### <a name="cause"></a>Orsak
Det här felet kan inträffa eftersom gatewayen har tagits bort eller associerade gatewaynyckeln har har återskapats.

#### <a name="resolution"></a>Lösning
Om gatewayen har tagits bort, återskapa gateway från portalen klickar du på **registrera**, kopiera nyckeln från portalen, klistra in den och försök att registrera gatewayen.

Om gatewayen finns fortfarande, men dess nyckel har tagits återskapas, kan du använda den nya nyckeln för att registrera gatewayen. Om du inte har nyckeln för att återskapa nyckeln igen från portalen.

### <a name="7-problem"></a>7. Problem
När du registrerat en gateway kan behöva du ange sökvägen och lösenord för ett certifikat.

![Ange certifikat](media/data-factory-troubleshoot-gateway-issues/specify-certificate.png)

#### <a name="cause"></a>Orsak
Gatewayen har registrerats på andra datorer innan. Under registreringen av en gateway har ett krypteringscertifikat associerats med gatewayen. Certifikatet kan vara själva genereras av gatewayen eller anges av användaren.  Det här certifikatet används för att kryptera autentiseringsuppgifter för datalagret (länkade tjänst).  

![Exportera certifikatet](media/data-factory-troubleshoot-gateway-issues/export-certificate.png)

När du återställer gatewayen på en annan värddator, frågar Registreringsguiden för det här certifikatet kan dekryptera autentiseringsuppgifterna som krypterats med det här certifikatet.  Utan det här certifikatet autentiseringsuppgifterna inte kan dekrypteras av den nya gatewayen och efterföljande kopiera aktivitet körningar som är associerade med den här nya gateway misslyckas.  

#### <a name="resolution"></a>Lösning
Om du har exporterat certifikatet för autentiseringsuppgifter från den ursprungliga gatewaydatorn med hjälp av den **exportera** knappen på den **inställningar** fliken i Data Management Gateway Configuration Manager använder certifikatet Här.

Du kan inte hoppa över det här steget när du återställer en gateway. Om certifikatet saknas, måste du ta bort gatewayen från portalen och skapa en ny gateway igen.  Dessutom kan uppdatera alla länkade tjänster som är kopplade till gatewayen genom att skriva in deras autentiseringsuppgifter.

### <a name="8-problem"></a>8. Problem
Du kan se följande felmeddelande visas.

`Error: The remote server returned an error: (407) Proxy Authentication Required.`

#### <a name="cause"></a>Orsak
Det här felet uppstår när din gateway är i en miljö som kräver en HTTP-proxy för åtkomst till Internet-resurser eller autentisering proxylösenord har ändrats men det inte uppdateras i din gateway.

#### <a name="resolution"></a>Lösning
Följ instruktionerna i den [Proxy server-överväganden](#proxy-server-considerations) avsnitt i denna artikel och konfigurera proxyinställningar med Data Management Gateway Configuration Manager.

## <a name="gateway-is-online-with-limited-functionality"></a>Gatewayen är online med begränsade funktioner
### <a name="1-problem"></a>1. Problem
Du kan se status för gatewayen som online med begränsade funktioner.

#### <a name="cause"></a>Orsak
Du se status för gatewayen som online med begränsade funktioner för en av följande orsaker:

* Gateway kan inte ansluta till Molntjänsten via Azure Service Bus.
* Molntjänsten kan inte ansluta till gatewayen via Service Bus.

När gatewayen är online med begränsade funktioner, kan du inte använda guiden Kopiera Data Factory för att skapa data pipelines för att kopiera data till eller från lokala datalager. Som en tillfällig lösning kan använda du Data Factory-redigeraren i portalen, Visual Studio eller Azure PowerShell.

#### <a name="resolution"></a>Lösning
Lösning på problemet (online med begränsade funktioner) baserat på om en gateway kan inte ansluta till Molntjänsten eller på annat sätt. I följande avsnitt beskrivs dessa lösningar.

### <a name="2-problem"></a>2. Problem
Du ser följande fel.

`Error: Gateway cannot connect to cloud service through service bus`

![Gateway kan inte ansluta till Molntjänsten](media/data-factory-troubleshoot-gateway-issues/gateway-cannot-connect-to-cloud-service.png)

#### <a name="cause"></a>Orsak
Gateway kan inte ansluta till Molntjänsten via Service Bus.

#### <a name="resolution"></a>Lösning
Följ dessa steg för att få tillbaka online gatewayen:

1. Tillåt utgående regler på gatewaydatorn och företagets brandvägg för IP-adress. Du kan hitta IP-adresser från Windows-händelseloggen (ID == 401): ett försök gjordes att komma åt en socket på ett sätt som tillåts inte av åtkomstbehörigheterna XX. XX. XX. XX:9350.
* Konfigurera proxyinställningar på gateway. Finns det [Proxy server-överväganden](#proxy-server-considerations) information.
* Aktivera utgående port 5671 och 9350 9354 på både Windows-brandväggen på gateway-datorn och företagets brandvägg. Finns det [portar och brandväggen](#ports-and-firewall) information. Det här steget är valfritt men rekommenderas för prestanda.

### <a name="3-problem"></a>3. Problem
Du ser följande fel.

`Error: Cloud service cannot connect to gateway through service bus.`

#### <a name="cause"></a>Orsak
Ett tillfälligt fel i nätverksanslutningen.

#### <a name="resolution"></a>Lösning
Följ dessa steg för att få tillbaka online gatewayen:

1. Vänta några minuter, anslutningen återställs automatiskt när felet är åtgärdat.
* Om felet kvarstår startar du om gateway-tjänsten.

## <a name="failed-to-author-linked-service"></a>Det gick inte att skapa länkade tjänst
### <a name="problem"></a>Problem
Det här felet kan uppstå när du försöker använda Autentiseringshanteraren i portalen för att ange autentiseringsuppgifter för en ny länkade tjänst eller uppdatera autentiseringsuppgifterna för en befintlig länkad tjänst.

`Error: The data store '<Server>/<Database>' cannot be reached. Check connection settings for the data source.`

När du ser det här felet på inställningssidan för Data Management Gateway Configuration Manager kan se ut som följande skärmbild.

![Databasen kan inte nås](media/data-factory-troubleshoot-gateway-issues/database-cannot-be-reached.png)

#### <a name="cause"></a>Orsak
SSL-certifikatet kan ha varit förlorat på gateway-datorn. Gateway-datorn kan inte läsa in certifikatet för närvarande som används för SSL-kryptering. Du kan också se ett felmeddelande visas i händelseloggen som liknar följande meddelande.

 `Unable to get the gateway settings from cloud service. Check the gateway key and the network connection. (Certificate with thumbprint cannot be loaded.)`

#### <a name="resolution"></a>Lösning
Följ dessa steg för att lösa problemet:

1. Starta Data Management Gateway Configuration Manager.
2. Växla till fliken **Settings** (Inställningar).  
3. Klicka på den **ändra** om du vill ändra SSL-certifikatet.

   ![Ändra certifikat för](media/data-factory-troubleshoot-gateway-issues/change-button-ssl-certificate.png)
4. Välj ett nytt certifikat som SSL-certifikatet. Du kan använda ett SSL-certifikat som genereras av du eller en organisation.

   ![Ange certifikat](media/data-factory-troubleshoot-gateway-issues/specify-http-end-point.png)

## <a name="copy-activity-fails"></a>Kopiera aktiviteten misslyckas
### <a name="problem"></a>Problem
Märker du följande ”UserErrorFailedToConnectToSqlserver”-fel när du har skapat en pipeline i portalen.

`Error: Copy activity encountered a user error: ErrorCode=UserErrorFailedToConnectToSqlServer,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Cannot connect to SQL Server`

#### <a name="cause"></a>Orsak
Detta kan inträffa av olika anledningar och minskning varierar i enlighet med detta.

#### <a name="resolution"></a>Lösning
Tillåt utgående TCP-anslutningar via TCP-port/1433 på klientsidan Data Management Gateway innan du ansluter till en SQL-databas.

Kontrollera SQL Server brandväggsinställningar för Azure samt om måldatabasen är en Azure SQL database.

Se följande avsnitt för att testa anslutningen till lokala datalager.

## <a name="data-store-connection-or-driver-related-errors"></a>Datalager anslutningen och drivrutinen-relaterade fel
Om data som lagrar anslutningen och drivrutinen-relaterade fel visas gör du följande:

1. Starta Data Management Gateway Configuration Manager på gatewaydatorn.
2. Växla till den **diagnostik** fliken.
3. I **Testanslutningen**, Lägg till värdena för gateway-grupp.
4. Klicka på **Test** att se om du kan ansluta till den lokala datakällan från gateway-datorn med hjälp av anslutningsinformationen och autentiseringsuppgifterna. Om testanslutningen fortfarande misslyckas när du har installerat en drivrutin, ska du starta om gatewayen så att den använder den senaste ändringen.

![Testa anslutning i fliken diagnostik](media/data-factory-troubleshoot-gateway-issues/test-connection-in-diagnostics-tab.png)

## <a name="gateway-logs"></a>Gateway-loggarna
### <a name="send-gateway-logs-to-microsoft"></a>Skicka loggar med gateway till Microsoft
När du kontaktar Microsoft Support för att få hjälp med felsökning av problem med gateway, kan du bli ombedd att dela din gateway-loggarna. Du kan dela krävs gateway-loggarna med två knappen klick i Data Management Gateway Configuration Manager med versionen av gatewayen.    

1. Växla till den **diagnostik** fliken i Data Management Gateway Configuration Manager.

    ![Fliken för data Management Gatewaydiagnostik](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-diagnostics-tab.png)
2. Klicka på **skicka loggar** att se följande dialogruta.

    ![Data Management Gateway skicka loggar](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-dialog.png)
3. (Valfritt) Klicka på **visa loggar** att granska loggar i Loggboken.
4. (Valfritt) Klicka på **sekretess** att granska sekretesspolicyn för Microsoft web services.
5. När du är nöjd med vad du ska överföra klickar du på **skicka loggar** faktiskt skicka loggarna från de senaste sju dagarna till Microsoft för felsökning. Du bör se status för åtgärden Skicka loggar som visas i följande skärmbild.

    ![Data Management Gateway skicka loggar status](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-status.png)
6. När åtgärden är klar, visas en dialogruta som visas i följande skärmbild.

    ![Data Management Gateway skicka loggar status](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-result.png)
7. Spara den **rapport-ID** och dela den med Microsoft-supporten. Rapport-ID används för att hitta gateway-loggarna som du har överfört för felsökning.  ID för rapporten sparas även i visningsprogrammet.  Du kan hitta den genom att titta på händelse-ID ”25” och kontrollera datum och tid.

    ![Data Management Gateway skicka loggar rapport-ID](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-report-id.png)    

### <a name="archive-gateway-logs-on-gateway-host-machine"></a>Arkivera gateway-loggar på värddatorn för gateway
Det finns vissa scenarier där du har problem med gateway och du kan inte dela gateway-loggarna direkt:

* Du kan manuellt installera gateway och registrera gatewayen.
* Du försöker registrera gatewayen med en återskapade nyckeln i Data Management Gateway Configuration Manager.
* Du försöker skicka loggar och gateway-värdtjänsten går inte att ansluta.

För dessa scenarier kan du spara gateway-loggarna som en zip-fil och dela den när du kontaktar Microsoft support. Till exempel om du får ett felmeddelande när du registrera gatewayen som visas i följande skärmbild.   

![Data Management Gateway registreringsfel](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-registration-error.png)

Klicka på den **Arkivera gateway-loggarna** länk för att arkivera och spara loggar och sedan dela zip-filen med Microsoft-supporten.

![Data Management Gateway Arkivera loggar](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-archive-logs.png)

### <a name="locate-gateway-logs"></a>Leta reda på gateway-loggarna
Du hittar detaljerad gateway logginformation i händelseloggarna i Windows.

1. Starta Windows **Loggboken**.
2. Leta reda på loggarna i den **program- och tjänstloggar** > **Data Management Gateway** mapp.

 När du felsöker problem med gateway leta efter felhändelser i Loggboken.

![Data Management Gateway loggas i Loggboken](media/data-factory-troubleshoot-gateway-issues/gateway-logs-event-viewer.png)
