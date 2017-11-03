---
title: "Aktivera Fjärrskrivbord i en Azure-molntjänst | Microsoft Docs"
description: "Hur du konfigurerar ditt tjänstprogram för azure-molnet för att tillåta anslutningar till fjärrskrivbord"
services: cloud-services
documentationcenter: 
author: thraka
manager: timlt
editor: 
ms.assetid: d3110ee8-6526-4585-aba5-d0bc9a713e9b
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: adegeo
ms.openlocfilehash: 413e72e9a39fcde84f56bfc61a6bc72dbadf1c97
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services"></a>Aktivera anslutning till fjärrskrivbord för en roll i Azure-molntjänster

> [!div class="op_single_selector"]
> * [Azure Portal](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [Klassisk Azure-portal](cloud-services-role-enable-remote-desktop.md)
> * [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Visual Studio](../vs-azure-tools-remote-desktop-roles.md)

Du kan aktivera anslutning till fjärrskrivbord i din roll under utvecklingen genom att inkludera Remote Desktop-moduler i tjänstedefinitionsfilen eller kan du aktivera fjärrskrivbord via Remote Desktop-tillägget. Det bästa sättet är att använda Remote Desktop-tillägget som du kan aktivera fjärrskrivbord även när programmet distribueras utan att behöva distribuera programmet.

## <a name="configure-remote-desktop-from-the-azure-classic-portal"></a>Konfigurera anslutning till fjärrskrivbord från den klassiska Azure-portalen
Den klassiska Azure-portalen använder metoden Remote Desktop-tillägget så att du kan aktivera fjärrskrivbord även när programmet distribueras. Den **konfigurera** på sidan för din molntjänst kan du ändra det lokala administratörskontot som används för att ansluta till virtuella datorer om du vill aktivera Fjärrskrivbord, certifikatet används i autentisering och ange förfallodatum.

1. Klicka på **molntjänster**, klicka på namnet på Molntjänsten och klicka sedan på **konfigurera**.
2. Klicka på den **Remote** längst ned.

    ![Fjärråtkomst-molntjänster](./media/cloud-services-role-enable-remote-desktop/CloudServices_Remote.png)

   > [!WARNING]
   > Alla rollinstanser kommer att startas om när du först aktivera Fjärrskrivbord och klicka på OK (markering). Certifikatet som används för att kryptera lösenordet måste installeras på vilken roll för att förhindra att en omstart. För att förhindra en omstart [överföra ett certifikat för Molntjänsten](cloud-services-configure-ssl-certificate.md#step-3-upload-a-certificate) och återgå sedan till den här dialogrutan.

3. I **roller**, väljer du den roll som du vill uppdatera eller välj **alla** för alla roller.
4. Gör något av följande ändringar:

   * Välj för att aktivera Fjärrskrivbord på **aktivera Fjärrskrivbord** kryssrutan. Avmarkera kryssrutan om du vill inaktivera Fjärrskrivbord.
   * Skapa ett konto som ska användas i anslutning till fjärrskrivbord till rollinstanserna.
   * Uppdatera lösenordet för det befintliga kontot.
   * Välj en överförda certifikat ska användas för autentisering (Överför certifikat med hjälp av **överför** på den **certifikat** sidan) eller skapa ett nytt certifikat.
   * Ändra utgångsdatumet för Remote Desktop-konfigurationen.

5. När du är klar configuration-uppdateringar, klickar du på **OK** (markering).

## <a name="remote-into-role-instances"></a>Remote i rollinstanser
När fjärrskrivbord är aktiverat på rollerna kan du fjärråtkomst till en rollinstans via olika verktyg.

Att ansluta till en rollinstans från den klassiska Azure-portalen:

1. Klicka på **instanser** att öppna den **instanser** sidan.
2. Välj en rollinstans som har konfigurerats för fjärrskrivbord.
3. Klicka på **Anslut**, och följ instruktionerna för att öppna skrivbordet.
4. Klicka på **öppna** och sedan **Anslut** fjärrskrivbord-anslutningen ska upprättas.

### <a name="use-visual-studio-to-remote-into-a-role-instance"></a>Använd Visual Studio för att till en rollinstans
I Visual Studio Server Explorer:

1. Expandera den **Azure** > **molntjänster** > **[molntjänstnamnet]** nod.
2. Expandera antingen **mellanlagring** eller **produktion**.
3. Expandera enskilda rollen.
4. Högerklicka på en av rollinstanserna klickar du på **ansluta med hjälp av fjärrskrivbord...** , och ange användarnamn och lösenord.

![Fjärrskrivbord i Server explorer](./media/cloud-services-role-enable-remote-desktop/ServerExplorer_RemoteDesktop.png)

### <a name="use-powershell-to-get-the-rdp-file"></a>Använd PowerShell för att hämta RDP-filen
Du kan använda den [Get-AzureRemoteDesktopFile](https://msdn.microsoft.com/library/azure/dn495261.aspx) för att hämta RDP-filen. Du kan sedan använda RDP-filen med anslutning till fjärrskrivbord för att få åtkomst till Molntjänsten.

### <a name="programmatically-download-the-rdp-file-through-the-service-management-rest-api"></a>Hämta programmässigt RDP-filen via Service Management REST API
Du kan använda den [ladda ned RDP-filen](https://msdn.microsoft.com/library/jj157183.aspx) REST-åtgärden för att ladda ned RDP-filen.

## <a name="to-configure-remote-desktop-in-the-service-definition-file"></a>Konfigurera fjärrskrivbord i tjänstdefinitionsfilen
Den här metoden kan du aktivera Fjärrskrivbord för program under utveckling. Den här metoden kräver krypterade lösenord lagras i tjänstkonfigurationen av fil- och eventuella uppdateringar till konfigurationen av fjärrskrivbord kräver en omdistribution av programmet. Om du vill undvika dessa downsides bör du använda remote desktop tillägget baserat-metod som beskrivs ovan.  

Du kan använda Visual Studio för att [aktivera en fjärrskrivbordsanslutning](../vs-azure-tools-remote-desktop-roles.md) med service definition file-metoden.  
Stegen nedan beskriver de ändringar som krävs för service model-filer till aktivera Fjärrskrivbord. Visual Studio gör automatiskt dessa ändringar när du publicerar.

### <a name="set-up-the-connection-in-the-service-model"></a>Upprätta anslutningen i tjänstemodellen
Använd den **import** element att importera den **RemoteAccess** modulen och **RemoteForwarder** modulen till den [ServiceDefinition.csdef](cloud-services-model-and-package.md#csdef) filen.

Tjänstdefinitionsfilen bör likna exemplet nedan med den `<Imports>` element som har lagts till.

```xml
<ServiceDefinition name="<name-of-cloud-service>" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2013-03.2.0">
    <WebRole name="WebRole1" vmsize="Small">
        <Sites>
            <Site name="Web">
                <Bindings>
                    <Binding name="Endpoint1" endpointName="Endpoint1" />
                </Bindings>
            </Site>
        </Sites>
        <Endpoints>
            <InputEndpoint name="Endpoint1" protocol="http" port="80" />
        </Endpoints>
        <Imports>
            <Import moduleName="Diagnostics" />
            <Import moduleName="RemoteAccess" />
            <Import moduleName="RemoteForwarder" />
        </Imports>
    </WebRole>
</ServiceDefinition>
```
Den [ServiceConfiguration.cscfg](cloud-services-model-and-package.md#cscfg) fil bör likna följande exempel, notera den `<ConfigurationSettings>` och `<Certificates>` element. Det angivna certifikatet måste vara [har överförts till Molntjänsten](cloud-services-how-to-create-deploy.md#how-to-upload-a-certificate-for-a-cloud-service).

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceConfiguration serviceName="<name-of-cloud-service>" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="3" osVersion="*" schemaVersion="2013-03.2.0">
    <Role name="WebRole1">
        <Instances count="2" />
        <ConfigurationSettings>
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.Enabled" value="true" />
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountUsername" value="[name-of-user-account]" />
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountEncryptedPassword" value="[base-64-encrypted-user-password]" />
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountExpiration" value="[certificate-expiration]" />
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteForwarder.Enabled" value="true" />
        </ConfigurationSettings>
        <Certificates>
            <Certificate name="Microsoft.WindowsAzure.Plugins.RemoteAccess.PasswordEncryption" thumbprint="[certificate-thumbprint]" thumbprintAlgorithm="sha1" />
        </Certificates>
    </Role>
</ServiceConfiguration>
```


## <a name="additional-resources"></a>Ytterligare resurser
[Hur du konfigurerar molntjänster](cloud-services-how-to-configure.md)
[Cloud services vanliga frågor och svar - fjärrskrivbord](cloud-services-faq.md)
