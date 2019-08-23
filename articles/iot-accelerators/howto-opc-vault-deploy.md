---
title: Så här distribuerar du OPC Vault Certificate Management Service – Azure | Microsoft Docs
description: Så här distribuerar du OPC-valvet certifikat hanterings tjänst från grunden.
author: mregen
ms.author: mregen
ms.date: 08/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: b626f906738e3a6e65053408cfdc2002d459a6e2
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/22/2019
ms.locfileid: "69973862"
---
# <a name="build-and-deploy-the-opc-ua-certificate-management-service"></a>Bygg och distribuera OPC UA Certificate Management-tjänsten

I den här artikeln förklaras hur du distribuerar tjänsten OPC UA Certificate Management i Azure.

> [!NOTE]
> Mer information om distributions information och instruktioner finns i GitHub [OPC Vault](https://github.com/Azure/azure-iiot-opc-vault-service)-lagringsplatsen.

## <a name="prerequisites"></a>Förutsättningar

### <a name="install-required-software"></a>Installera nödvändig program vara

För närvarande är åtgärden för att bygga och distribuera begränsad till Windows.
Exemplen är skrivna för C# .net standard, vilket krävs för att bygga tjänsten och exempel för distribution.
Alla verktyg du behöver för .net standard levereras med .net Core-verktyg. [Här](https://docs.microsoft.com/dotnet/articles/core/getting-started) finns information om vad du behöver.

1. [Installera .net Core 2.1 +][dotnet-install].
2. [Installera Docker][docker-url] (valfritt, endast om den lokala Docker-versionen krävs).
4. Installera [Azures kommando rads verktyg för PowerShell][powershell-install].
5. Registrera dig för en [Azure-prenumeration][azure-free].

### <a name="clone-the-repository"></a>Klona lagringsplatsen

Om du inte har gjort det ännu kan du klona den här GitHub-lagringsplatsen.  Öppna en kommando tolk eller Terminal och kör:

```bash
git clone https://github.com/Azure/azure-iiot-opc-vault-service
cd azure-iiot-opc-vault-service 
```

eller klona lagrings platsen direkt i Visual Studio 2017.

### <a name="build-and-deploy-the-azure-service-on-windows"></a>Bygg och Distribuera Azure-tjänsten i Windows

Ett PowerShell-skript är ett enkelt sätt att distribuera OPC Vault mikrotjänster och programmet.<br>

1. Öppna ett PowerShell-fönster i lagrings platsen-roten. 
3. Gå till mappen Deploy`cd deploy`
3. Välj ett namn för `myResourceGroup` det är osannolikt att orsaka en konflikt med andra distribuerade webb sidor. Se [](#website-name-already-in-use) hur webb sidornas namn väljs utifrån namnet på resurs gruppen.
5. Starta distributionen med `.\deploy.ps1` för interaktiv installation<br>
eller ange en fullständig kommando rad:  
`.\deploy.ps1  -subscriptionName "MySubscriptionName" -resourceGroupLocation "East US" -tenantId "myTenantId" -resourceGroupName "myResourceGroup"`
7. Om du planerar att utveckla med den här distributionen lägger `-development 1` du till för att aktivera Swagger-användargränssnittet och distribuera fel söknings versioner.
6. Följ instruktionerna i skriptet för att logga in på prenumerationen och ange ytterligare information.
9. När du har skapat och distribuerat en lyckad åtgärd bör du se följande meddelande:

```
To access the web client go to:
https://myResourceGroup.azurewebsites.net

To access the web service go to:
https://myResourceGroup-service.azurewebsites.net

To start the local docker GDS server:
.\myResourceGroup-dockergds.cmd

To start the local dotnet GDS server:
.\myResourceGroup-gds.cmd
```
Om du stöter på problem följer du stegen [nedan](#troubleshooting-deployment-failures).

8. Öppna din favorit webbläsare och öppna program sidan:`https://myResourceGroup.azurewebsites.net`
8. Ge webbappen och OPC-valvet en mikrotjänst några minuter att värma upp efter distributionen. Webb start sidan kanske låser sig vid första användningen i upp till en minut tills du får de första svaren.
11. Ta en titt på Swagger API Open:`https://myResourceGroup-service.azurewebsites.net`
13. Starta en lokal GDS-server med dotNet Start `.\myResourceGroup-gds.cmd` eller med Docker Start `.\myResourceGroup-dockergds.cmd`.

Som sido anteckning är det möjligt att distribuera om en version med exakt samma inställningar. Tänk på att en sådan åtgärd förnyar alla program hemligheter och kan återställa vissa inställningar i program registreringarna i Azure Active Directory (Azure AD).

Det går också att distribuera om bara webbappens binärfiler. Med parameterns `-onlyBuild 1` nya zip-paket och appen distribueras till webb programmen.

När distributionen är klar är du välkommen att börja använda tjänsterna: [Så här hanterar du certifikat hanterings tjänsten för OPC Vault](howto-opc-vault-manage.md)

## <a name="delete-the-certificate-management-services-from-the-subscription"></a>Ta bort certifikat hanterings tjänsterna från prenumerationen

1. Logga in på Azure Portal: `https://portal.azure.com`.
2. Gå till resurs gruppen där tjänsten distribuerades.
3. Välj `Delete resource group` och bekräfta.
4. Efter en kort stund har alla distribuerade tjänst komponenter tagits bort.
5. Gå nu till `Azure Active Directory/App registrations`.
6. Det bör finnas tre registreringar för varje distribuerad resurs grupp med följande namn: `resourcegroup-client`, `resourcegroup-module`, `resourcegroup-service`.
Varje registrering måste tas bort separat.
7. Nu tas alla distribuerade komponenter bort.

## <a name="troubleshooting-deployment-failures"></a>Felsöka distributions fel

### <a name="resource-group-name"></a>Resursgruppsnamn

Se till att du använder ett kort och enkelt resurs grupp namn.  Namnet används också för att namnge resurser och URL-prefixet för tjänsten och därför måste det uppfylla kraven för resurs namn.  

### <a name="website-name-already-in-use"></a>Webbplats namnet används redan

Det är möjligt att namnet på webbplatsen redan används.  Om du stöter på det här felet måste du använda ett annat resurs grupp namn. Värd namnen som används av distributions skriptet är: https://resourcegroupname.azurewebsites.net och. https://resourgroupname-service.azurewebsites.net
Andra namn på tjänster skapas med kombinationen av korta namn-hashar och är sannolikt inte en konflikt med andra tjänster.

### <a name="azure-active-directory-azure-ad-registration"></a>Azure Active Directory (Azure AD)-registrering 

Distributions skriptet försöker registrera tre Azure AD-program i Azure Active Directory.  
Den här åtgärden kan Miss förväntas beroende på dina behörigheter i den valda Azure AD-klienten.   Det finns två alternativ:

1. Om du väljer en Azure AD-klient från en lista över klienter startar du om skriptet och väljer ett annat i listan.
2. Du kan också distribuera en privat Azure AD-klient i en annan prenumeration, starta om skriptet och välja att använda det.

## <a name="deployment-script-options"></a>Distributions skript alternativ

Skriptet använder följande parametrar:


```
-resourceGroupName
```

Kan vara namnet på en befintlig eller en ny resurs grupp.

```
-subscriptionId
```


Valfritt, prenumerations-ID där resurser ska distribueras.

```
-subscriptionName
```


Eller alternativt prenumerations namnet.

```
-resourceGroupLocation
```


Valfritt, en resurs grupps plats. Om det här alternativet anges försöker att skapa en ny resurs grupp på den här platsen.


```
-tenantId
```


Azure AD-klient som ska användas. 

```
-development 0|1
```

Valfritt, för att distribuera för utveckling. Använd fel söknings version och Ställ in ASP.Net-miljön på utveckling. Skapa '. publishsettings ' för import i Visual Studio 2017 så att den kan distribuera appen och tjänsten direkt.

```
-onlyBuild 0|1
```

Valfritt, för att återskapa och omdistribuera bara webbappar och återskapa Docker-behållare.

[azure-free]:https://azure.microsoft.com/free/
[powershell-install]:https://azure.microsoft.com/downloads/#powershell
[docker-url]: https://www.docker.com/
[dotnet-install]: https://www.microsoft.com/net/learn/get-started

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du distribuerar OPC-valvet från grunden är det föreslagna nästa steg:

> [!div class="nextstepaction"]
> [Hantera OPC-valv](howto-opc-vault-manage.md)
