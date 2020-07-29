---
title: Konfigurera en instans och autentisering (skript)
titleSuffix: Azure Digital Twins
description: Se så här konfigurerar du en instans av tjänsten Azure Digitals dubbla nätverk, inklusive rätt autentisering. Skript version.
author: baanders
ms.author: baanders
ms.date: 7/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 522096b921faf34130f0c37f727d89c7bf95c530
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87337916"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-scripted"></a>Konfigurera en digital Azure-instans och autentisering (skript)

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

Den här artikeln beskriver steg för steg hur du **konfigurerar en ny Azure Digital-instansen**, inklusive hur du skapar instansen och konfigurerar autentisering. När du har slutfört den här artikeln har du en Azure Digital-instansen som är redo att starta programmering mot.

Den här versionen av den här artikeln slutför de här stegen genom att köra ett [ **skript exempel för automatiserad distribution** ](https://docs.microsoft.com/samples/azure-samples/digital-twins-samples/digital-twins-samples/) som effektiviserar processen. Om du vill visa de manuella stegen som skriptet körs genom i bakgrunden går du till den manuella versionen av den här artikeln: [*anvisningar: Konfigurera en instans och autentisering (manuell)*](how-to-set-up-instance-manual.md).

[!INCLUDE [digital-twins-setup-starter.md](../../includes/digital-twins-setup-starter.md)]

## <a name="run-the-deployment-script"></a>Kör distributions skriptet

I den här artikeln används ett digitalt kod exempel för Azure för att distribuera en digital Digitals-instans i Azure och den nödvändiga autentiseringen är halv automatiskt. Det kan också användas som utgångs punkt för att skriva dina egna skriptbaserade interaktioner.

Exempel skriptet skrivs i PowerShell. Den är en del av [Azures digitala dubbla exempel](https://docs.microsoft.com/samples/azure-samples/digital-twins-samples/digital-twins-samples/), som du kan ladda ned till datorn genom att gå till den exempel länken och välja hämtnings knappen för att *Ladda ned ZIP* under rubriken.

I den hämtade exempel mappen finns distributions skriptet på _Azure_Digital_Twins_samples.zip > skript > **deploy.ps1** _.

Här följer stegen för att köra distributions skriptet i Cloud Shell.
1. Gå till ett [Azure Cloud Shell](https://shell.azure.com/) -fönster i webbläsaren. Logga in med det här kommandot:
    ```azurecli-interactive
    az login
    ```
    Om CLI kan öppna din standard webbläsare så gör den det och läser in en Azure-inloggnings sida. Annars öppnar du en webb sida på *https://aka.ms/devicelogin* och anger den auktoriseringskod som visas i din terminal.
 
2. När du har loggat in tittar du på det Cloud Shell fönstrets ikon fält. Välj ikonen "Ladda upp/ladda ned filer" och välj "Ladda upp".

    :::image type="content" source="media/how-to-set-up-instance/cloud-shell-upload.png" alt-text="Cloud Shell fönster som visar val av överförings alternativ":::

    Navigera till _**deploy.ps1**_ -filen på din dator och tryck på "öppna". Detta kommer att överföra filen till Cloud Shell så att du kan köra den i fönstret Cloud Shell.

3. Kör skriptet genom `./deploy.ps1` att skicka kommandot i Cloud Shells fönstret. När skriptet körs genom de automatiserade konfigurations stegen uppmanas du att skicka in följande värden:
    * För instansen: *prenumerations-ID* för din Azure-prenumeration som ska användas
    * För-instansen: en *plats* där du vill distribuera instansen. Om du vill se vilka regioner som stöder Azure Digitals, kan du gå till [*Azure-produkter som är tillgängliga efter region*](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins).
    * För instansen: ett *resurs grupp* namn. Du kan använda en befintlig resurs grupp eller ange ett nytt namn på en som ska skapas.
    * För-instansen: ett *namn* för din Azure Digital-instansen. Namnet på den nya instansen måste vara unikt inom regionen för din prenumeration (vilket innebär att om din prenumeration har en annan Azure Digital-instans i den region som redan använder det namn du väljer, blir du ombedd att välja ett annat namn).
    * För app-registreringen: ett *visnings namn för Azure AD-program* som ska associeras med registreringen. Den här appens registrering är den plats där du konfigurerar åtkomst behörigheter till [Azure Digitals dubbla API: er](how-to-use-apis-sdks.md). Senare kommer klient programmet att autentiseras mot appens registrering och därför beviljas de konfigurerade åtkomst behörigheterna till API: erna.
    * För app-registreringen: en *Azure AD Application svars-URL* för Azure AD-programmet. Du kan använda `http://localhost` .

Skriptet skapar en Azure Digital-instansen, tilldelar din Azure-användare rollen *Azure Digitals-ägare (för hands version)* på instansen och konfigurerar en Azure AD-App-registrering för din klient app som ska användas.

Här är ett utdrag från utmatnings loggen från skriptet:

:::image type="content" source="media/how-to-set-up-instance/deployment-script-output.png" alt-text="Cloud Shell fönstret visar logg över indata och utdata genom körningen av distributions skriptet" lightbox="media/how-to-set-up-instance/deployment-script-output.png":::

Om skriptet har slutförts kommer den slutliga utskriften att stå `Deployment completed successfully` . Annars kan du åtgärda fel meddelandet och köra skriptet igen. De steg som du redan har slutfört kommer att kringgås och börja begära inmatningar vid den punkt där du slutade.

När skriptet har slutförts har du nu en Azure Digital-instansen som är redo att användas och behörigheter som ställts in för att hantera den.

## <a name="collect-important-values"></a>Samla in viktiga värden

Det finns två viktiga värden från den app-registrering som kommer att behövas senare för att [autentisera en klient app mot Azures digitala dubbla API: er](how-to-authenticate-client.md). 

Du hittar dem genom att följa [den här länken](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) för att gå till översikts sidan för Azure AD-appen registrering i Azure Portal. Den här sidan visar alla app-registreringar som har skapats i din prenumeration.

Du bör se den app-registrering som du nyss skapade i den här listan. Välj den för att öppna informationen:

:::image type="content" source="media/how-to-set-up-instance/app-important-values.png" alt-text="Portal visning av viktiga värden för appens registrering":::

Anteckna *program* -ID och *katalog (klient)-ID: t* som **visas på sidan** . Om du inte är den person som ska skriva kod för klient program måste du dela dessa värden med den person som ska vara.

## <a name="verify-success"></a>Verifieringen lyckades

Om du vill kontrol lera skapandet av dina resurser och behörigheter som har skapats av skriptet kan du titta på dem i [Azure Portal](https://portal.azure.com).

### <a name="verify-instance"></a>Verifiera instans

För att kontrol lera att din instans har skapats går du till [sidan Azure Digitals dubbla](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.DigitalTwins%2FdigitalTwinsInstances) i Azure Portal. Den här sidan visar alla dina Azure Digitals dubbla instanser. Leta efter namnet på den nyligen skapade instansen i listan.

### <a name="verify-user-role-assignment"></a>Verifiera tilldelning av användar roll

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

### <a name="verify-app-registration"></a>Verifiera registrering av appar

[!INCLUDE [digital-twins-setup-verify-app-registration-1.md](../../includes/digital-twins-setup-verify-app-registration-1.md)]

Kontrol lera först att inställningarna för Azure Digitals-inställningar för digital har kon figurer ATS korrekt i registreringen. Det gör du genom att välja *manifest* från meny raden för att visa appens registrerings manifest kod. Bläddra till slutet av kod fönstret och leta efter dessa fält under `requiredResourceAccess` . Värdena ska matcha dem i skärm bilden nedan:

[!INCLUDE [digital-twins-setup-verify-app-registration-2.md](../../includes/digital-twins-setup-verify-app-registration-2.md)]

## <a name="other-possible-steps-for-your-organization"></a>Andra möjliga steg för din organisation

[!INCLUDE [digital-twins-setup-additional-requirements.md](../../includes/digital-twins-setup-additional-requirements.md)]

## <a name="next-steps"></a>Nästa steg

Se hur du ansluter klient programmet till din instans genom att skriva klient appens autentiseringsnyckel:
* [*Instruktion: skriva kod för app-autentisering*](how-to-authenticate-client.md)
