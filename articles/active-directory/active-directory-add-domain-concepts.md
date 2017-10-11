---
title: "Översikt över anpassade domännamn i Azure Active Directory | Microsoft Docs"
description: "Beskriver konceptuella framework för att använda anpassade domännamn i Azure Active directory, inklusive federation för enkel inloggning"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: fd0c5def-0da2-43af-81bc-76f4cfe86afd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2017
ms.author: curtand
ms.openlocfilehash: 3c591680160101a91174868714392674c9aa7178
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2017
---
# <a name="conceptual-overview-of-custom-domain-names-in-azure-active-directory"></a>Översikt över anpassade domännamn i Azure Active Directory
Ett domännamn kan vara en viktig identifierare för många directory-resurser som en del av:

* Ett användarnamn eller e-postadress för en användare
* Adressen för en grupp
* App-ID URI för ett program

En resurs i Azure Active Directory (Azure AD) kan innehålla ett domännamn som redan har verifierats som ägs av den katalog som innehåller resursen. Endast en global administratör kan utföra hanteringsuppgifter för domänen i Azure AD.

> [!IMPORTANT]
> Microsoft rekommenderar att du hanterar Azure AD via [Azure AD administratörscenter](https://aad.portal.azure.com) på Azure Portal istället för via den klassiska Azure-portalen som nämns i den här artikeln. Att hantera dina domännamn i Azure AD-administrationscentret, se [hantera anpassade domännamn i Azure Active Directory](active-directory-domains-manage-azure-portal.md).

Domännamn i Azure AD är globalt unika. Ett anpassat domännamn kan användas av endast en Azure AD-klient i taget. Om en Azure AD-katalog har verifierat att ett domännamn, kan sedan inga andra Azure AD-katalog verifiera eller använder samma domännamnet.

## <a name="initial-and-custom-domain-names"></a>Inledande och anpassade domännamn
Varje domännamn i Azure AD är antingen ett första domännamn eller ett anpassat domännamn.

Varje Azure AD innehåller ett första domännamn i formuläret contoso.onmicrosoft.com. Det här tredje nivån domännamnet i det här exemplet ”contoso.onmicrosoft.com” har upprättats när katalogen skapas vanligtvis av administratören som skapade katalogen. Det ursprungliga domännamnet för en katalog kan inte ändras eller tas bort. Det ursprungliga domännamnet är medan fullständigt funktionella avsedd att användas som en mekanism för bootstrapping förrän ett anpassat domännamn har verifierats.

En katalog har minst en verifierad domän, till exempel ”contoso.com” i de flesta produktionsmiljöer, och det är den anpassa domänen som är synligt för användarna. Ett anpassat domännamn är ett domännamn som ägs och används av den organisationen, till exempel ”contoso.com” för använder som värd för webbplatsen. Det här domännamnet är bekant för anställda eftersom det är en del av användarnamnet som de använder för att logga in i företagsnätverket, eller för att skicka och hämta e-post.

Innan den kan användas av Azure AD, kan det anpassade domännamnet måste läggas till din katalog och verifieras.

## <a name="verified-and-unverified-domain-names"></a>Verifierade och overifierade domännamn
Det ursprungliga domännamnet för en katalog utvärderas implicit som verifierats av Azure AD. När en administratör lägger till ett anpassat domännamn i en Azure AD, är det ursprungligen overifierade tillståndet. Azure AD kan inte några katalogresurser så att den använder en overifierade domän. Detta säkerställer att endast en katalog kan använda ett visst domännamn och att organisationen använder domännamnet äger domännamnet.

Azure AD verifierar ägarskap för ett domännamn genom att söka efter en särskild post i domain name service (DNS)-zonfilen för domännamnet. Om du vill verifiera ditt ägarskap till ett domännamn, hämtar administratör DNS-posten från Azure AD att Azure AD att söka efter och lägger till posten i DNS-zonfilen för domännamnet. DNS-zonfilen underhålls av domännamnsregistratorn för domänen. Stegen för att verifiera en domän som visas i artikeln för [att lägga till en anpassad domän till Azure AD-katalogen](active-directory-add-domain.md).

Lägga till en post i DNS-zonfilen för domännamnet påverkar inte andra domäntjänster, till exempel e-post eller webbhotell.

## <a name="federated-and-managed-domain-names"></a>Federerade och hanterade domännamn
Ett anpassat domännamn i Azure AD kan konfigureras för att ge användare en federerad inloggning i upplevelse mellan din lokala Active Directory och Azure AD. Konfigurera en domän för federation måste uppdateringar till Privilegierade resurser i Azure AD och att din Windows Server Active Directory. Konfigurera en federerad domän måste utföras från Azure AD Connect eller med hjälp av PowerShell. Federering en anpassad domän kan inte initieras från den klassiska Azure-portalen. [Titta på det här videoklippet och lär dig mer om hur du konfigurerar AD FS för användaren loggar in med Azure AD Connect](http://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Configuring-AD-FS-for-user-sign-in-with-Azure-AD-Connect).

Domäner som inte är federerat kallas ibland hanterade domäner. Den första domänen för en Azure AD-katalog är implicit utvärderas som en hanterad domän.

## <a name="primary-domain-names"></a>Primära domännamn
Primära domännamnet för en katalog är det domännamn som är förvald som standardvärde för '' domändel av användarnamnet, när en administratör skapar en ny användare i den [Azure-portalen](https://portal.azure.com/), eller en annan portal som den Administrationsportalen för Office 365 eller Microsoft Intune-portalen. En katalog kan ha endast en primär domännamn. En administratör kan ändra namnet på primär domän ska vara någon verifierad domän som inte är federerat, eller till den första domänen.

## <a name="domain-names-in-azure-ad-and-other-microsoft-online-services"></a>Domännamn i Azure AD och andra Microsoft Online Services
Ett domännamn måste verifieras i Azure AD innan den kan användas av en annan Microsoft Online Service som Exchange Online, SharePoint Online och Intune. Följande tjänster kräver normalt en administratör för att lägga till en eller flera DNS-poster som är specifika för tjänsten.

En Azure-webbapp använder sin egen mekanism för att verifiera ditt ägarskap till en domän. En domän måste verifieras för användning med Azure AD, även om det tidigare har verifierats för användning av en Azure webbapp i en prenumeration som förlitar sig på att Azure AD. En Azure-app kan använda ett domännamn som har verifierats i en annan katalog från katalogen som skyddar webbprogrammet.

## <a name="managing-domain-names"></a>Hantera domännamn
Hanteringsuppgifter för domänen kan utföras från den klassiska Azure portalen och PowerShell. Många aktiviteter kan utföras med hjälp av Azure AD Graph API.

* [Lägga till och verifiera ett eget domännamn](active-directory-add-domain.md)
* [Hantera domäner i den klassiska Azure-portalen](active-directory-add-manage-domain-names.md)
* [Använda PowerShell för att hantera domännamn i Azure AD](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)
* [Använda Azure AD Graph API för att hantera domännamn i Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations)

