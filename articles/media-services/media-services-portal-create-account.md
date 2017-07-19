---
title: Skapa ett Azure Media Services-konto med Azure-portalen | Microsoft Docs
description: "Den här självstudien går igenom hur du skapar ett Azure Media Services-konto med Azure-portalen."
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: c551e158-aad6-47b4-931e-b46260b3ee4c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/10/2017
ms.author: juliako
ms.translationtype: HT
ms.sourcegitcommit: 2ad539c85e01bc132a8171490a27fd807c8823a4
ms.openlocfilehash: 9ab5368c173f016f44546d6c8acb360598f5f5ab
ms.contentlocale: sv-se
ms.lasthandoff: 07/12/2017

---
# <a name="create-an-azure-media-services-account-using-the-azure-portal"></a>Skapa ett Media Services-konto med Azure-portalen
> [!div class="op_single_selector"]
> * [Portalen](media-services-portal-create-account.md)
> * [PowerShell](media-services-manage-with-powershell.md)
> * [REST](https://docs.microsoft.com/rest/api/media/mediaservice)
> 
> [!NOTE]
> Du behöver ett Azure-konto för att slutföra den här självstudien. Mer information om den [kostnadsfria utvärderingsversionen av Azure](https://azure.microsoft.com/pricing/free-trial/). 
> 
> 

Azure-portalen är ett sätt att snabbt skapa ett Azure Media Services-konto (AMS). Du kan använda ditt konto för att få åtkomst till Media Services för att lagra, kryptera, koda, hantera och strömma medieinnehåll i Azure. När du skapar ett Media Services-konto skapar du också ett associerat lagringskonto (eller använder ett befintligt) i samma geografiska område som Media Services-kontot.

Den här artikeln beskriver några vanliga begrepp och visar hur du skapar ett Media Services-konto med Azure-portalen.

## <a name="concepts"></a>Koncept
Åtkomst till Media Services kräver två associerade konton:

* Ett Media Services-konto. Med ditt konto får du åtkomst till en uppsättning molnbaserade Media Services-tjänster som är tillgängliga i Azure. På ett Media Services-konto lagras inget faktiskt medieinnehåll. I stället lagras metadata om medieinnehåll och mediebearbetningsjobb på ditt konto. När du skapar kontot väljer du en tillgänglig Media Services-region. Den regionen som du väljer är ett datacenter som lagrar metadataposterna för ditt konto.
  
* Ett Azure-lagringskonto. Lagringskonton måste finnas i samma geografiska område som Media Services-kontot. När du skapar ett Media Services-konto kan du antingen välja ett befintligt lagringskonto i samma region eller skapa ett nytt lagringskonto i samma region. Om du tar bort ett Media Services-konto raderas inte blobbarna på ditt relaterade lagringskonto.

> [!NOTE]
> Information om tillgänglighet för Azure Media Services-funktioner i olika regioner finns i [tillgänglighet för AMS-funktioner i datacenter](scenarios-and-availability.md#a-idavailabilitya-availability-of-media-services-features-across-datacenters).

## <a name="create-an-ams-account"></a>Skapa ett AMS-konto
Stegen i det här avsnittet visar hur du skapar ett AMS-konto.

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Klicka på **+Ny** > **Webb + mobilt** > **Media Services**.
   
    ![Skapa Media Services](./media/media-services-create-account/media-services-new1.png)
3. Ange de erfordrade värdena i **SKAPA MEDIA SERVICES-KONTO**.
   
    ![Skapa Media Services](./media/media-services-create-account/media-services-new3.png)
   
   1. Ange namnet på det nya AMS-kontot vid **Kontonamn**. Namnet på ett Media Services-konto består av gemena bokstäver eller siffror utan blanksteg och 3 till 24 tecken.
   2. Vid Prenumeration väljer du mellan de olika Azure-prenumerationer som du har åtkomst till.
   3. I **Resursgrupp** väljer du ny eller befintlig resurs.  En resursgrupp är en samling resurser som delar livscykel, behörigheter och principer. Lär dig mer [här](../azure-resource-manager/resource-group-overview.md#resource-groups).
   4. För **Plats** väljer du den geografiska region som ska användas för att lagra media och metadataposter för ditt Media Services-konto. Den här regionen används för att bearbeta och strömma dina media. Endast de tillgängliga Media Services-regionerna visas i listrutan. 
   5. Vid **Storage-konto** väljer du ett lagringskonto för att tillhandahålla Blob Storage av medieinnehållet från ditt Media Services-konto. Du kan välja ett befintligt lagringskonto i samma geografiska region som ditt Media Services-konto eller skapa ett lagringskonto. Ett nytt lagringskonto skapas i samma region. Reglerna för namn på lagringskonton är desamma som för Media Services-konton.
      
       Mer information om lagring finns [här](../storage/storage-introduction.md).
   6. Välj **PIN-kod för instrumentpanelen** för att se förloppet för kontodistributionen.
4. Klicka på **Skapa** längst ned i formuläret.
   
    När kontot har skapats läses översiktssidan in. I tabellen med slutpunkter för direktuppspelning har kontot en standardslutpunkt för direktuppspelning med tillståndet **Stoppad**. 

    >[!NOTE]
    >När ditt AMS-konto skapas läggs en **standard**-slutpunkt för direktuppspelning till på ditt konto med tillståndet **Stoppad**. Om du vill starta direktuppspelning av innehåll och dra nytta av dynamisk paketering och dynamisk kryptering måste slutpunkten för direktuppspelning som du vill spela upp innehåll från ha tillståndet **Körs**. 
   
## <a name="to-manage-your-ams-account"></a>Hantera AMS-kontot

Om du vill hantera ditt AMS-konto (till exempel ansluta till AMS-API:et via programmering, överföra videoklipp, koda tillgångar, konfigurera innehållsskydd och övervaka jobbförlopp) väljer du **Inställningar** på vänster sida av portalen. I **Inställningar** navigerar du till något av de tillgängliga bladen (till exempel **API-åtkomst**, **Tillgångar**, **Jobb** eller **Innehållsskydd**).


## <a name="next-steps"></a>Nästa steg

Du kan nu överföra filer till AMS-kontot. Mer information finns i [Överföra filer](media-services-portal-upload-files.md).

Om du tänker ansluta till AMS-API:et via programmering kan du läsa [Access the Azure Media Services API with Azure AD authentication](media-services-use-aad-auth-to-access-ams-api.md) (Ansluta API:et för Azure Media Services med Azure AD-autentisering).

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


