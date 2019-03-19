---
title: Felsökning av problem med Gateway för datahantering | Microsoft Docs
description: Innehåller tips för att felsöka problem med Gateway för datahantering.
services: data-factory
author: nabhishek
manager: craigg
ms.assetid: c6756c37-4e5a-4d1e-ab52-365f149b4128
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/01/2017
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 0559d89bd691323a95713d518df05e58283cef39
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58119351"
---
# <a name="troubleshoot-issues-with-using-data-management-gateway"></a>Felsöka problem med gateway för datahantering
Den här artikeln innehåller information om hur du felsöker problem med Gateway för datahantering.

> [!NOTE]
> Den här artikeln gäller för version 1 av Azure Data Factory. Om du använder den aktuella versionen av Data Factory-tjänsten finns i [lokal IR i Data Factory](../create-self-hosted-integration-runtime.md).

Se den [Data Management Gateway](data-factory-data-management-gateway.md) artikeln för detaljerad information om gatewayen. Se den [flytta data mellan lokala och molnbaserade](data-factory-move-data-between-onprem-and-cloud.md) artikeln en genomgång för att flytta data från en lokal SQL Server-databas till Microsoft Azure Blob storage med hjälp av gatewayen.

## <a name="failed-to-install-or-register-gateway"></a>Det gick inte att installera eller registrera gateway
### <a name="1-problem"></a>1. Problem
Du ser det här felmeddelandet när du installerar och registrerar en gateway, särskilt vid nedladdning av gateway-installationsfilen.

`Unable to connect to the remote server". Please check your local settings (Error Code: 10003).`

#### <a name="cause"></a>Orsak
Den dator som du försöker installera gatewayen kunde inte ladda ned den senaste gateway-klientinstallationsfilen från download center på grund av ett nätverksproblem.

#### <a name="resolution"></a>Lösning
Kontrollera inställningarna för brandväggen proxyservern om du vill se om inställningarna blockera nätverksanslutningen från datorn till den [hämtningssidan](https://download.microsoft.com/), och uppdatera inställningarna i enlighet med detta.

Du kan också hämta installationsfilen för den senaste gatewayen från den [hämtningssidan](https://www.microsoft.com/download/details.aspx?id=39717) på andra datorer som har åtkomst till download center. Du kan kopiera installationsfilen till gateway-värddatorn och köra den manuellt för att installera och uppdatera gatewayen.

### <a name="2-problem"></a>2. Problem
Du ser det här felet när du försöker installera en gateway genom att klicka på **installera direkt på den här datorn** i Azure-portalen.

`Error:  Abort installing a new gateway on this computer because this computer has an existing installed gateway and a computer without any installed gateway is required for installing a new gateway.`  

#### <a name="cause"></a>Orsak
En gateway har installerats på datorn.

#### <a name="resolution"></a>Lösning
Avinstallera den befintliga gatewayen på datorn och klicka på den **installera direkt på den här datorn** länka igen.

### <a name="3-problem"></a>3. Problem
Du kan se det här felet när du registrerar en ny gateway.

`Error: The gateway has encountered an error during registration.`

#### <a name="cause"></a>Orsak
Det här meddelandet kan visas för någon av följande orsaker:

* Formatet på den gateway-nyckeln är ogiltig.
* Gateway-nyckel är inte längre giltig.
* Gateway-nyckeln har återskapats från portalen.  

#### <a name="resolution"></a>Lösning
Kontrollera om du använder rätt gateway-nyckel från portalen. Om det behövs kan återskapa en nyckel och använda nyckeln för att registrera gatewayen.

### <a name="4-problem"></a>4. Problem
Du kan se ett felmeddelande när du registrerar en gateway.

`Error: The content or format of the gateway key "{gatewayKey}" is invalid, please go to azure portal to create one new gateway or regenerate the gateway key.`



![Innehållet eller formatet för nyckel är ogiltig](media/data-factory-troubleshoot-gateway-issues/invalid-format-gateway-key.png)

#### <a name="cause"></a>Orsak
Innehållet eller formatet för den inkommande gateway-nyckeln är felaktigt. En av anledningarna kan vara att du har kopierat endast en del av nyckeln från portalen eller om du använder en ogiltig nyckel.

#### <a name="resolution"></a>Lösning
Skapa en gateway-nyckel i portalen och Använd kopieringsknappen för att kopiera hela nyckeln. Klistra in den i det här fönstret för att registrera gatewayen.

### <a name="5-problem"></a>5. Problem
Du kan se ett felmeddelande när du registrerar en gateway.

`Error: The gateway key is invalid or empty. Specify a valid gateway key from the portal.`

![Gateway-nyckel är ogiltig eller tom](media/data-factory-troubleshoot-gateway-issues/gateway-key-is-invalid-or-empty.png)

#### <a name="cause"></a>Orsak
Gateway-nyckeln har återskapats eller gatewayen har tagits bort i Azure-portalen. Det kan också inträffa om Data Management Gateway-installationen inte är senast.

#### <a name="resolution"></a>Lösning
Kontrollera om Data Management Gateway-installationen är den senaste versionen kan du hitta den senaste versionen på Microsofts [hämtningssidan](https://go.microsoft.com/fwlink/p/?LinkId=271260).

Om installationen är aktuella / senaste och gateway fortfarande finns på portalen, återskapa nyckeln gateway i Azure-portalen och Använd kopieringsknappen för att kopiera hela nyckeln och klistra in den i det här fönstret för att registrera gatewayen. I annat fall återskapa gatewayen och börja om från början.

### <a name="6-problem"></a>6. Problem
Du kan se ett felmeddelande när du registrerar en gateway.

`Error: Gateway has been online for a while, then shows “Gateway is not registered” with the status “Gateway key is invalid”`

![Gateway-nyckel är ogiltig eller tom](media/data-factory-troubleshoot-gateway-issues/gateway-not-registered-key-invalid.png)

#### <a name="cause"></a>Orsak
Det här felet kan inträffa antingen gatewayen har tagits bort eller den associera gateway-nyckeln har återskapats.

#### <a name="resolution"></a>Lösning
Om gatewayen har tagits bort, återskapa gatewayen från portalen, klickar på **registrera**, kopiera nyckeln från portalen, klistra in den och försök att registrera gatewayen.

Om gatewayen fortfarande att finnas kvar, men dess nyckel har återskapats, kan du använda den nya nyckeln för att registrera gatewayen. Om du inte har nyckeln kan du återskapa nyckeln igen från portalen.

### <a name="7-problem"></a>7. Problem
När du registrerat en gateway kan behöva du ange sökvägen och lösenord för ett certifikat.

![Ange certifikat](media/data-factory-troubleshoot-gateway-issues/specify-certificate.png)

#### <a name="cause"></a>Orsak
Gatewayen har registrerats på andra datorer innan. Under den första registreringen för en gateway för har ett krypteringscertifikat associerats med gatewayen. Certifikatet kan vara lokal genereras av gatewayen eller anges av användaren.  Det här certifikatet används för att kryptera autentiseringsuppgifterna för datalagret (länkad tjänst).  

![Exportera certifikatet](media/data-factory-troubleshoot-gateway-issues/export-certificate.png)

När du återställer en gateway på en annan värddator, frågar Registreringsguiden för det här certifikatet dekryptera autentiseringsuppgifterna som krypterats med det här certifikatet.  Autentiseringsuppgifterna som inte kan dekrypteras av den nya gatewayen och efterföljande kopiera aktivitet körningar som är associerade med den här nya gatewayen misslyckas utan det här certifikatet.  

#### <a name="resolution"></a>Lösning
Om du har exporterat certifikatet för autentiseringsuppgifter från den ursprungliga gatewaydatorn med hjälp av den **exportera** knappen på den **inställningar** fliken i Data Management Gateway Configuration Manager, använda certifikat Här.

Du kan inte hoppa över det här steget när du återställer en gateway. Om certifikatet saknas, måste du ta bort gatewayen från portalen och skapa en ny gateway.  Dessutom kan uppdatera alla länkade tjänster som är relaterade till gatewayen genom att du slipper ange autentiseringsuppgifter.

### <a name="8-problem"></a>8. Problem
Du kan se ett felmeddelande.

`Error: The remote server returned an error: (407) Proxy Authentication Required.`

#### <a name="cause"></a>Orsak
Det här felet inträffar när din gateway är i en miljö som kräver en HTTP-proxy för åtkomst till Internet-resurser eller autentisering proxylösenordet har ändrats men den inte uppdateras i din gateway.

#### <a name="resolution"></a>Lösning
Följ anvisningarna i avsnittet Proxy server överväganden i den här artikeln och konfigurera proxyinställningar med Data Management Gateway Configuration Manager.

## <a name="gateway-is-online-with-limited-functionality"></a>Gatewayen är online med begränsad funktionalitet
### <a name="1-problem"></a>1. Problem
Du kan se statusen för gatewayen som online med begränsad funktionalitet.

#### <a name="cause"></a>Orsak
Du se statusen för gatewayen som online med begränsad funktionalitet för någon av följande orsaker:

* Gatewayen kan inte ansluta till Molntjänsten via Azure Service Bus.
* Molntjänsten kan inte ansluta till gatewayen via Service Bus.

När gatewayen är online med begränsad funktionalitet, kan du inte att kunna använda Data Factory-Kopieringsguiden skapa datapipelines för att kopiera data till eller från lokala datalager. Som en lösning kan använda du Data Factory-redigeraren i portal, Visual Studio eller Azure PowerShell.

#### <a name="resolution"></a>Lösning
Lösning på problemet (online med begränsad funktionalitet) baseras på om gatewayen inte kan ansluta till Molntjänsten eller tvärtom. Följande avsnitt innehåller dessa lösningar.

### <a name="2-problem"></a>2. Problem
Du ser följande fel.

`Error: Gateway cannot connect to cloud service through service bus`

![Gatewayen kan inte ansluta till Molntjänsten](media/data-factory-troubleshoot-gateway-issues/gateway-cannot-connect-to-cloud-service.png)

#### <a name="cause"></a>Orsak
Gatewayen kan inte ansluta till Molntjänsten via Service Bus.

#### <a name="resolution"></a>Lösning
Följ dessa steg om du vill hämta gatewayen online igen:

1. Tillåt IP-adress utgående regler på gatewaydatorn och företagets brandvägg. Du hittar IP-adresser från händelseloggen i Windows (ID == 401): Ett försök gjordes att komma åt en socket på ett sätt som tillåts inte av åtkomstbehörigheterna XX. XX. XX. XX:9350.
1. Konfigurera proxyinställningar på gatewayen. Se avsnittet Proxy server överväganden för information.
1. Aktivera utgående portarna 5671 och 9350-9354 på båda Windows brandväggen på gatewaydatorn och företagets brandvägg. I avsnittet portar och brandväggen för information. Det här steget är valfritt men rekommenderas för prestanda.

### <a name="3-problem"></a>3. Problem
Du ser följande fel.

`Error: Cloud service cannot connect to gateway through service bus.`

#### <a name="cause"></a>Orsak
Ett tillfälligt fel i nätverksanslutningen.

#### <a name="resolution"></a>Lösning
Följ dessa steg om du vill hämta gatewayen online igen:

1. Vänta några minuter, anslutningen återställs automatiskt när felet är borta.
1. Om felet kvarstår startar du om gateway-tjänsten.

## <a name="failed-to-author-linked-service"></a>Det gick inte att skapa länkad tjänst
### <a name="problem"></a>Problem
Det här felet kan uppstå när du försöker använda Autentiseringshanteraren i portalen för att ange autentiseringsuppgifter för en ny länkad tjänst eller uppdatera autentiseringsuppgifterna för en befintlig länkad tjänst.

`Error: The data store '<Server>/<Database>' cannot be reached. Check connection settings for the data source.`

När det här felet visas inställningssidan för Data Management Gateway Configuration Manager kan se ut som på följande skärmbild.

![Databasen kan inte nås](media/data-factory-troubleshoot-gateway-issues/database-cannot-be-reached.png)

#### <a name="cause"></a>Orsak
SSL-certifikatet kan ha varit förlorat på gateway-datorn. Gateway-datorn kan inte läsa in certifikatet för närvarande som används för SSL-kryptering. Du kan även se ett felmeddelande visas i händelseloggen som liknar följande meddelande.

 `Unable to get the gateway settings from cloud service. Check the gateway key and the network connection. (Certificate with thumbprint cannot be loaded.)`

#### <a name="resolution"></a>Lösning
Följ dessa steg för att lösa problemet:

1. Starta Data Management Gateway Configuration Manager.
2. Växla till fliken **Settings** (Inställningar).  
3. Klicka på den **ändra** knappen för att ändra SSL-certifikatet.

   ![Ändra certifikat för](media/data-factory-troubleshoot-gateway-issues/change-button-ssl-certificate.png)
4. Välj ett nytt certifikat som SSL-certifikat. Du kan använda ett SSL-certifikat som genereras av dig eller en organisation.

   ![Ange certifikat](media/data-factory-troubleshoot-gateway-issues/specify-http-end-point.png)

## <a name="copy-activity-fails"></a>Kopieringen misslyckas
### <a name="problem"></a>Problem
Du kanske märker följande ”UserErrorFailedToConnectToSqlserver”-fel när du har skapat en pipeline i portalen.

`Error: Copy activity encountered a user error: ErrorCode=UserErrorFailedToConnectToSqlServer,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Cannot connect to SQL Server`

#### <a name="cause"></a>Orsak
Detta kan inträffa av olika skäl och minskning varierar i enlighet med detta.

#### <a name="resolution"></a>Lösning
Tillåt utgående TCP-anslutningar över port TCP/1433 på klientsidan för Data Management Gateway innan du ansluter till en SQL-databas.

Kontrollera SQL Server brandväggsinställningar för Azure samt om måldatabasen är en Azure SQL database.

Se följande avsnitt för att testa anslutningen till det lokala datalagringen.

## <a name="data-store-connection-or-driver-related-errors"></a>Datalager-anslutning eller drivrutinen-relaterade fel
Om du ser data lagrar anslutning eller drivrutinen-relaterade fel gör du följande:

1. Starta Konfigurationshanteraren för Data Management Gateway på gateway-datorn.
2. Växla till den **diagnostik** fliken.
3. I **Testanslutningen**, Lägg till värdena för gateway-grupp.
4. Klicka på **Test** att se om du kan ansluta till en lokal datakälla från gateway-datorn med anslutningsinformationen och autentiseringsuppgifterna. Om testanslutningen fortfarande misslyckas när du har installerat en drivrutin, ska du starta om gatewayen så att den använder den senaste ändringen.

![Testa anslutning i fliken diagnostik](media/data-factory-troubleshoot-gateway-issues/test-connection-in-diagnostics-tab.png)

## <a name="gateway-logs"></a>Gateway-loggar
### <a name="send-gateway-logs-to-microsoft"></a>Skicka gateway-loggar till Microsoft
När du kontaktar Microsoft Support att få hjälp med felsökning av problem med gateway kan du bli ombedd att dela din gateway-loggar. Med denna version av gatewayen är kan du dela krävs gateway-loggar med två knapptryckningar i Data Management Gateway Configuration Manager.    

1. Växla till den **diagnostik** fliken i Data Management Gateway Configuration Manager.

    ![Fliken för data Management Gateway-diagnostik](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-diagnostics-tab.png)
2. Klicka på **skicka loggar** att se följande dialogruta.

    ![Data Management Gateway skicka loggar](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-dialog.png)
3. (Valfritt) Klicka på **visa loggar** att granska loggarna i Loggboken.
4. (Valfritt) Klicka på **sekretess** läsa sekretesspolicy för Microsoft web services.
5. När du är nöjd med vad du kommer att ladda upp, klickar du på **skicka loggar** faktiskt skicka loggarna från de senaste sju dagarna till Microsoft för felsökning. Du bör se status för åtgärden Skicka loggarna som du ser i följande skärmbild.

    ![Data Management Gateway skicka loggar status](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-status.png)
6. När åtgärden har slutförts, visas en dialogruta som visas i följande skärmbild.

    ![Data Management Gateway skicka loggar status](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-result.png)
7. Spara den **rapport-ID** och dela den med Microsoft Support. Rapport-ID används för att leta reda på gatewayloggarna som du laddade upp för felsökning.  Rapport-ID också sparas i visningsprogrammet.  Du kan hitta den genom att titta på händelse-ID ”25” och kontrollera datum och tid.

    ![Data Management Gateway skicka loggar rapport-ID](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-report-id.png)    

### <a name="archive-gateway-logs-on-gateway-host-machine"></a>Arkivera gateway loggar in på värddatorn för gateway
Det finns vissa scenarier där du har problem med gateway och du kan inte dela gateway-loggar direkt:

* Du kan manuellt installera gatewayen och registrera gatewayen.
* Du försöker registrera gatewayen med en återskapade nyckeln i Data Management Gateway Configuration Manager.
* Du försöker skicka loggar och gatewaytjänsten kan inte anslutas.

Dessa scenarier kan du spara gateway-loggar som en zip-fil och dela den när du kontaktar Microsoft support. Till exempel om du får ett felmeddelande när du registrerar gatewayen som visas i följande skärmbild.   

![Data Management Gateway registreringsfel](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-registration-error.png)

Klicka på den **Arkivera gateway-loggar** länk för att arkivera och spara loggar och sedan dela zip-filen med Microsoft-supporten.

![Data Management Gateway-arkivet loggar](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-archive-logs.png)

### <a name="locate-gateway-logs"></a>Leta reda på gatewayloggarna
Du hittar detaljerad gateway logginformation i Windows-händelseloggar.

1. Starta Windows **Loggboken**.
2. Leta reda på loggarna i den **program- och tjänstloggar** > **Data Management Gateway** mapp.

   När du felsöker gateway-relaterade problem, leta efter felnivåhändelser i Loggboken.

![Data Management Gateway-loggarna i Loggboken](media/data-factory-troubleshoot-gateway-issues/gateway-logs-event-viewer.png)
