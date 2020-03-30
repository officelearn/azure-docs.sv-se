---
title: Distribuera tjänsten OPC Vault-certifikathantering – Azure | Microsoft-dokument
description: Så här distribuerar du certifikathanteringstjänsten OPC Vault från grunden.
author: mregen
ms.author: mregen
ms.date: 08/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: f577059e1ebf70e3a9dfe9e538a9d3d49d7c8e96
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "71200007"
---
# <a name="build-and-deploy-the-opc-vault-certificate-management-service"></a>Skapa och distribuera tjänsten för hantering av OPC Vault-certifikat

I den här artikeln beskrivs hur du distribuerar OPC Vault-certifikathanteringstjänsten i Azure.

> [!NOTE]
> Mer information finns i [GitHub OPC Vault-databasen](https://github.com/Azure/azure-iiot-opc-vault-service).

## <a name="prerequisites"></a>Krav

### <a name="install-required-software"></a>Installera nödvändig programvara

För närvarande är bygg- och distributionsåtgärden begränsad till Windows.
Exemplen är alla skrivna för C# .NET Standard, som du behöver för att skapa tjänsten och exempel för distribution.
Alla verktyg du behöver för .NET Standard levereras med .NET Core-verktygen. Se [Kom igång med .NET Core](https://docs.microsoft.com/dotnet/articles/core/getting-started).

1. [Installera .NET Core 2.1+][dotnet-install].
2. [Installera Docker][docker-url] (valfritt, endast om den lokala Docker-versionen krävs).
4. Installera [Azure-kommandoradsverktygen för PowerShell][powershell-install].
5. Registrera dig för en [Azure-prenumeration][azure-free].

### <a name="clone-the-repository"></a>Klona lagringsplatsen

Om du inte har gjort det ännu klonar du den här GitHub-databasen. Öppna en kommandotolk eller terminal och kör följande:

```bash
git clone https://github.com/Azure/azure-iiot-opc-vault-service
cd azure-iiot-opc-vault-service 
```

Alternativt kan du klona reporäntan direkt i Visual Studio 2017.

### <a name="build-and-deploy-the-azure-service-on-windows"></a>Skapa och distribuera Azure-tjänsten i Windows

Ett PowerShell-skript är ett enkelt sätt att distribuera OPC Vault-mikrotjänsten och programmet.

1. Öppna ett PowerShell-fönster vid reporoten. 
3. Gå till distributionsmappen `cd deploy`.
3. Välj ett `myResourceGroup` namn för det är osannolikt att orsaka en konflikt med andra distribuerade webbsidor. Se avsnittet "Webbplatsnamn som redan används" senare i den här artikeln.
5. Starta distributionen `.\deploy.ps1` med för interaktiv installation eller ange en fullständig kommandorad:  
`.\deploy.ps1  -subscriptionName "MySubscriptionName" -resourceGroupLocation "East US" -tenantId "myTenantId" -resourceGroupName "myResourceGroup"`
7. Om du planerar att utveckla `-development 1` med den här distributionen lägger du till för att aktivera Swagger-användargränssnittet och distribuera felsökningsversioner.
6. Följ instruktionerna i skriptet för att logga in på din prenumeration och för att ge ytterligare information.
9. Efter en lyckad bygg- och distributionsåtgärd bör du se följande meddelande:
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

   > [!NOTE]
   > Om det uppstår problem läser du avsnittet "Felsöka distributionsfel" senare i artikeln.

8. Öppna din favoritwebbläsare och öppna programsidan:`https://myResourceGroup.azurewebsites.net`
8. Ge webbappen och OPC Vault-mikrotjänsten några minuter att värma upp efter distributionen. Webbsidan kan hänga på första användningen, i upp till en minut, tills du får de första svaren.
11. Om du vill ta en titt på Swagger API öppnar du:`https://myResourceGroup-service.azurewebsites.net`
13. Om du vill starta en lokal `.\myResourceGroup-gds.cmd`GDS-server med dotnet startar du . Med Docker, `.\myResourceGroup-dockergds.cmd`börja .

Det är möjligt att distribuera om en version med exakt samma inställningar. Tänk på att en sådan åtgärd förnyar alla programhemligheter och kan återställa vissa inställningar i Azure Active Directory (Azure AD) programregistreringar.

Det är också möjligt att distribuera om bara webbappbinärerna. Med parametern `-onlyBuild 1`distribueras nya zip-paket av tjänsten och appen till webbprogrammen.

Efter en lyckad distribution kan du börja använda tjänsterna. Se [Hantera tjänsten för hantering av OPC Vault-certifikat](howto-opc-vault-manage.md).

## <a name="delete-the-services-from-the-subscription"></a>Ta bort tjänsterna från prenumerationen

Så här gör du:

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Gå till resursgruppen där tjänsten distribuerades.
3. Välj **Ta bort resursgrupp** och bekräfta.
4. Efter en kort stund tas alla distribuerade tjänstkomponenter bort.
5. Gå till **Azure Active Directory** > **App registreringar**.
6. Det bör finnas tre registreringar som anges för varje distribuerad resursgrupp. Registreringarna har följande `resourcegroup-client`namn: `resourcegroup-module` `resourcegroup-service`, . Ta bort varje registrering separat.

Nu tas alla distribuerade komponenter bort.

## <a name="troubleshooting-deployment-failures"></a>Felsöka distributionsfel

### <a name="resource-group-name"></a>Namn på resursgrupp

Använd ett kort och enkelt resursgruppsnamn. Namnet används också för att namnge resurser och tjänstens URL-prefix. Som sådan måste den uppfylla resursnamngivningskraven.  

### <a name="website-name-already-in-use"></a>Webbplatsnamn som redan används

Det är möjligt att namnet på webbplatsen redan används. Du måste använda ett annat resursgruppsnamn. De värdnamn som används av distributionsskriptet är: https://resourcegroupname.azurewebsites.net och https://resourgroupname-service.azurewebsites.net.
Andra namn på tjänster byggs av en kombination av kortnamn hashar, och är osannolikt att stå i konflikt med andra tjänster.

### <a name="azure-ad-registration"></a>Azure Active Directory-registrering 

Distributionsskriptet försöker registrera tre Azure AD-program i Azure AD. Beroende på dina behörigheter i den valda Azure AD-klienten kan den här åtgärden misslyckas. Det finns två alternativ:

- Om du väljer en Azure AD-klientorganisation från en lista över klienter startar du om skriptet och väljer en annan från listan.
- Du kan också distribuera en privat Azure AD-klientorganisation i en annan prenumeration. Starta om skriptet och välj att använda det.

## <a name="deployment-script-options"></a>Alternativ för distributionsskript

Skriptet tar följande parametrar:


```
-resourceGroupName
```

Detta kan vara namnet på en befintlig eller en ny resursgrupp.

```
-subscriptionId
```


Det här är prenumerations-ID där resurser ska distribueras. Det är valfritt.

```
-subscriptionName
```


Du kan också använda prenumerationsnamnet.

```
-resourceGroupLocation
```


Det här är en resursgruppsplats. Om det anges försöker den här parametern skapa en ny resursgrupp på den här platsen. Den här parametern är också valfri.


```
-tenantId
```


Detta är Azure AD-klienten att använda. 

```
-development 0|1
```

Detta är att distribuera för utveckling. Använd felsökningsversion och ange ASP.NET-miljön till utveckling. Skapa `.publishsettings` för import i Visual Studio 2017 så att den kan distribuera appen och tjänsten direkt. Den här parametern är också valfri.

```
-onlyBuild 0|1
```

Detta är att bygga om och distribuera om endast webbappar och att återskapa Docker-behållarna. Den här parametern är också valfri.

[azure-free]:https://azure.microsoft.com/free/
[powershell-install]:https://azure.microsoft.com/downloads/#powershell
[docker-url]: https://www.docker.com/
[dotnet-install]: https://www.microsoft.com/net/learn/get-started

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du distribuerar OPC Vault från grunden kan du:

> [!div class="nextstepaction"]
> [Hantera OPC Vault](howto-opc-vault-manage.md)
