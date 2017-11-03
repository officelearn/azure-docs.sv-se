---
title: "Azure Diagnostics-tillägget configuration schemat versioner och historik | Microsoft Docs"
description: "Relevant för att samla in prestandaräknarna i Azure Virtual Machines, Skalningsuppsättningar, Service Fabric och Cloud Services."
services: monitoring-and-diagnostics
documentationcenter: .net
author: rboucher
manager: carmonm
editor: 
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/16/2017
ms.author: robb
ms.openlocfilehash: 119e8a237f24cdc80a1ab8e376f2b308c9eada05
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="azure-diagnostics-extention-configuration-schema-versions-and-history"></a>Azure Diagnostics-tillägget configuration schemat versioner och historik
Den här sidan index Azure Diagnostics tillägget schemat versioner levereras som en del av Microsoft Azure SDK.  

> [!NOTE]
> Tillägget för Azure-diagnostik är den komponent som används för att samla in prestandaräknare och annan statistik från:
> - Azure Virtual Machines 
> - Skalningsuppsättningar för Virtual Machines
> - Service Fabric 
> - Molntjänster 
> - Nätverkssäkerhetsgrupper
> 
> Den här sidan gäller endast om du använder någon av dessa tjänster.

Azure-diagnostik-tillägget används med andra Microsoft-produkter för diagnostik som Azure-Monitor, Application Insights och logganalys. Mer information finns i [övervakning verktyg översikt över Microsoft](monitoring-overview.md).

## <a name="azure-sdk-and-diagnostics-versions-shipping-chart"></a>Azure SDK och diagnostik versioner leverans diagram  

|Azure SDK-version | Version för diagnostik-tillägg | Modellen|  
|------------------|-------------------------------|------|  
|1.x               |1.0                            |plugin-program|  
|2.0 - 2.4         |1.0                            |plugin-program|  
|2.5               |1.2                            |Tillägg|  
|2.6               |1.3                            |"|  
|2.7               |1.4                            |"|  
|2.8               |1.5                            |"|  
|2.9               |1.6                            |"|
|2.96              |1.7                            |"|
|2.96              |1.8                            |"|
|2.96              |1.8.1                          |"|
|2.96              |1.9                            |"|



 Azure Diagnostics version 1.0 första leverans plugin modellen – vilket innebär att när du installerade Azure SDK om du har fått versionen av Azure diagnostics levereras med den.  

 Från och med SDK 2.5 (diagnostics version 1.2), Azure diagnostics skickades till en extension-modell. Verktyg för att använda nya funktioner bara var tillgängliga i den nyare Azure SDK, men alla tjänster med hjälp av Azure-diagnostik vill hämta den senaste versionen av leverans direkt från Azure. Till exempel skulle alla som fortfarande använder SDK 2.5 laddas den senaste versionen som visas i föregående tabell, oavsett om de använder de nya funktionerna.  

## <a name="schemas-index"></a>Scheman index  
Konfiguration av olika scheman används för olika versioner av Azure-diagnostik. 

[Diagnostik 1.0 Konfigurationsschemat](azure-diagnostics-schema-1dot0.md)  

[Diagnostik 1.2 Konfigurationsschemat](azure-diagnostics-schema-1dot2.md)  

[Diagnostik 1.3 och senare Konfigurationsschemat](azure-diagnostics-schema-1dot3-and-later.md)  

## <a name="version-history"></a>Versionshistorik


### <a name="diagnostics-extension-19"></a>Diagnostik tillägget 1.9. 
Stöd har lagts till Docker.


### <a name="diagnostics-extension-181"></a>Diagnostik tillägget 1.8.1 
Ange en SAS-token i stället för en lagringskontonyckel i privata konfig. Om en SAS-token har angetts, ignoreras lagringskontots åtkomstnyckel.


```json
{
    "storageAccountName": "diagstorageaccount",
    "storageAccountEndPoint": "https://core.windows.net",
    "storageAccountSasToken": "{sas token}",
    "SecondaryStorageAccounts": {
        "StorageAccount": [
            {
                "name": "secondarydiagstorageaccount",
                "endpoint": "https://core.windows.net",
                "sasToken": "{sas token}"
            }
        ]
    }
}
```

```xml
<PrivateConfig>
    <StorageAccount name="diagstorageaccount" endpoint="https://core.windows.net" sasToken="{sas token}" />
    <SecondaryStorageAccounts>
        <StorageAccount name="secondarydiagstorageaccount" endpoint="https://core.windows.net" sasToken="{sas token}" />
    </SecondaryStorageAccounts>
</PrivateConfig>
```


### <a name="diagnostics-extension-18"></a>Diagnostik tillägget 1.8 
Tillagda lagringstyp som ska PublicConfig. StorageType kan vara *tabell*, *Blob*, *TableAndBlob*. *Tabellen* är standard.


```json
{
    "WadCfg": {
    },
    "StorageAccount": "diagstorageaccount",
    "StorageType": "TableAndBlob"
}
```

```xml
<PublicConfig>
    <WadCfg />
    <StorageAccount>diagstorageaccount</StorageAccount>
    <StorageType>TableAndBlob</StorageType>
</PublicConfig>
```


### <a name="diagnostics-extension-17"></a>Diagnostik tillägget 1.7 
Möjligheten att vidarebefordra till EventHub har lagts till.

### <a name="diagnostics-extension-15"></a>Diagnostik tillägget 1.5
Lägga till elementet sänkor och möjlighet att skicka diagnostikdata till [Programinsikter](../application-insights/app-insights-cloudservices.md) vilket gör det enklare att diagnostisera problem i ditt program, samt nivån system och infrastruktur.

### <a name="azure-sdk-26-and-diagnostics-extension-13"></a>Azure SDK 2.6 och diagnostik tillägget 1.3 
Följande ändringar har gjorts för Cloud Service-projekt i Visual Studio. (Ändringarna gäller även för senare versioner av Azure SDK.)

* Lokala emulatorn stöder nu diagnostik. Det innebär att du kan samla in diagnostikdata och se till att programmet skapar rätt spåren när du utvecklar och testar i Visual Studio. Anslutningssträngen `UseDevelopmentStorage=true` aktiverar diagnostik datainsamling när du kör cloud service-projekt i Visual Studio med hjälp av Azure storage-emulatorn. Alla diagnostikdata samlas in i storage-konto (utveckling lagring).
* Diagnostik konto lagringsanslutningssträngen (Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString) lagras igen i tjänstekonfigurationsfilen (.cscfg). I Azure SDK 2.5 har diagnostik storage-konto angetts i filen diagnostics.wadcfgx.

Det finns några viktiga skillnader mellan hur anslutningssträngen fungerade i Azure SDK 2.4 och tidigare och hur det fungerar i Azure SDK 2.6 eller senare.

* I Azure SDK 2.4 och tidigare användes anslutningssträngen som en körning av diagnostik plugin-programmet att hämta information om storage-konto för överföring av diagnostik loggar.
* Azure SDK 2.6 och senare, används anslutningssträngen diagnostik av Visual Studio för att konfigurera diagnostik-tillägget med lämplig lagring kontoinformation vid publicering. Anslutningssträngen kan du definiera olika lagringskonton för olika konfigurationer som Visual Studio ska använda när du publicerar. Eftersom diagnostik plugin-programmet inte längre är tillgängliga (efter Azure SDK 2.5) kan inte .cscfg-filen ensamt aktivera tillägget diagnostik. Du måste aktivera tillägget separat via verktyg som Visual Studio eller PowerShell.
* Paketet utdata från Visual Studio innehåller också den offentliga konfigurationen-XML för diagnostik-tillägget för varje roll för att förenkla konfigureringen av tillägget diagnostik med PowerShell. Visual Studio använder anslutningssträngen diagnostik för att fylla kontoinformationen lagring finns i offentliga konfiguration. Den offentliga konfigurationsfiler skapas i mappen tillägg och följer mönstret PaaSDiagnostics. <RoleName>. PubConfig.xml. PowerShell-baserade distributioner kan använda det här mönstret för att mappa varje konfiguration till en roll.
* Anslutningssträngen i .cscfg-filen används också av Azure portal dataåtkomst diagnostik så visas det i den **övervakning** fliken. Anslutningssträngen behövs för att konfigurera tjänsten om du vill visa utförliga övervakningsdata i portalen.

#### <a name="migrating-projects-to-azure-sdk-26-and-later"></a>Migrera projekt till Azure SDK 2.6 eller senare
När du migrerar från Azure SDK 2.5 Azure SDK 2.6 eller senare om du har en diagnostiklagringskonto som anges i filen .wadcfgx, sedan förblir den det. Om du vill dra nytta av möjlighet att använda olika lagringskonton för olika lagringskonfigurationer, har du manuellt lägga till anslutningssträngen i projektet. Om du migrerar ett projekt från Azure SDK 2.4 eller tidigare till Azure SDK 2.6, bevaras anslutningssträngar diagnostik. Observera dock ändringar i hur anslutningssträngar behandlas i Azure SDK 2.6 som anges i föregående avsnitt.

#### <a name="how-visual-studio-determines-the-diagnostics-storage-account"></a>Hur Visual Studio avgör diagnostik storage-konto
* Om en anslutningssträng för diagnostik anges i .cscfg-filen, används Visual Studio för att konfigurera diagnostik-tillägget när du publicerar och vid generering av xml-filerna offentliga konfiguration under paketering.
* Om inga diagnostik anslutningssträngen har angetts i .cscfg-filen, sedan faller Visual Studio tillbaka till med hjälp av storage-konto som anges i filen .wadcfgx för att konfigurera diagnostik-tillägget när du publicerar och generera xml-filerna offentliga konfiguration när paketera.
* Anslutningssträngen diagnostik i .cscfg-filen har företräde framför storage-konto i filen .wadcfgx. Om en anslutningssträng för diagnostik anges i .cscfg-filen, Visual Studio använder som och ignorerar lagringskonto i .wadcfgx.

#### <a name="what-does-the-update-development-storage-connection-strings-checkbox-do"></a>Vad gör de ”uppdatering development storage-anslutningssträngarna...” kryssrutan för?
Kryssrutan för **uppdatera development storage-anslutningssträngar för diagnostik- och cachelagring med Microsoft Azure lagringskontouppgifter vid publicering till Microsoft Azure** ger dig ett enkelt sätt att uppdatera alla development lagringskontots anslutningssträngar med Azure storage-konto som angetts under publiceringen.

Anta att du väljer den här kryssrutan och diagnostik anslutningssträngen anger `UseDevelopmentStorage=true`. När du publicerar projektet till Azure, uppdateras automatiskt diagnostik anslutningssträngen med storage-konto som du angav i guiden Publicera i Visual Studio. Men om en verklig lagringskonto har angetts som anslutningssträng diagnostik, används kontot i stället.

### <a name="diagnostics-functionality-differences-between-azure-sdk-24-and-earlier-and-azure-sdk-25-and-later"></a>Diagnostik funktioner skillnaderna mellan Azure SDK 2.4 och tidigare och Azure SDK 2,5 och senare
Om du uppgraderar ditt projekt från Azure SDK 2.4 till Azure SDK 2.5 eller senare, bör du ha i åtanke följande diagnostik funktioner skillnader.

* **Konfiguration av API: er är inaktuella** – programkonfiguration av diagnostik är tillgänglig i Azure SDK 2.4 eller tidigare versioner, men är föråldrad i Azure SDK 2.5 och senare. Om diagnostik konfigurationen har definierats i koden, måste du konfigurera om dessa inställningar från grunden i projektet migrerade diagnostik för att fortsätta arbeta. Konfigurationsfilen diagnostik för Azure SDK 2.4 är diagnostics.wadcfg och diagnostics.wadcfgx för Azure SDK 2.5 och senare.
* **Diagnostik för molnprogram för tjänsten kan bara konfigureras på rollnivå, inte på instansnivå.**
* **Varje gång du distribuerar appen, diagnostik konfiguration uppdateras** – om du ändrar konfigurationen diagnostik från Server Explorer och distribuera din app kan det medföra problem för paritet.
* **Azure SDK 2.5 och senare, krascher Dumpar konfigureras i konfigurationsfilen diagnostik inte i koden** – om du har krascher Dumpar som konfigurerats i koden, måste du överföra konfigurationen från koden till manuellt i konfigurationsfilen eftersom krascher Dumpar inte överförs under migreringen till Azure SDK 2.6.

