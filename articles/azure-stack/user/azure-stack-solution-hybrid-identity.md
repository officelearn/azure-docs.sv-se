---
title: Konfigurera hybrid cloud-identitet med Azure och Azure Stack | Microsoft Docs
description: Lär dig hur du konfigurerar hybrid cloud-identitet med Azure och Azure Stack-program.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/14/2019
ms.author: mabrigg
ms.reviewer: anajod
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: d36b94ac9f130649e880e6bff1fa3a8b223828b4
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2019
ms.locfileid: "55244416"
---
# <a name="tutorial-configure-hybrid-cloud-identity-for-azure-and-azure-stack-applications"></a>Självstudier: Konfigurera hybrid cloud-identitet för Azure och Azure Stack-program

*Gäller för: Integrerade Azure Stack-system och Azure Stack Development Kit*

Lär dig hur du konfigurerar en hybrid cloud-identitet för dina Azure och Azure Stack-program.

Har du två alternativ för att bevilja åtkomst till dina program i både globala Azure och Azure Stack.

 * När Azure Stack har en kontinuerlig anslutning till Internet, kan du använda Azure Active Directory (AD Azure).
 * När Azure Stack är bortkopplad från Internet, kan du använda Azure Directory Federation Services (AD FS).

Du kan använda tjänstens huvudnamn för att bevilja åtkomst till dina Azure Stack-program för distribution eller konfigurationen med hjälp av Azure Resource Manager i Azure Stack.

I den här självstudien skapar du en exempel-miljö för att:

> [!div class="checklist"]
> - Upprätta en hybrididentitet i globala Azure och Azure Stack
> - Hämta en token för att få åtkomst till Azure Stack-API.

Du måste ha behörigheter för Azure Stack-operatör för stegen i den här självstudien.

> [!Tip]  
> ![hybrid-pillars.png](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> Microsoft Azure Stack är en utökning av Azure. Azure Stack får du flexibilitet och utvecklingsmöjligheterna med molnberäkning i din lokala miljö och aktivera det enda hybridmolnet som hjälper dig att skapa och distribuera hybridappar var som helst.  
> 
> Faktabladet [designöverväganden för Hybridtillämpningar](https://aka.ms/hybrid-cloud-applications-pillars) granskar grundpelare för programkvalitet (placering, skalbarhet, tillgänglighet, återhämtning, hantering och säkerhet) för att utforma, distribuera och driva hybrid program. Designöverväganden kan optimera hybrid programdesign, minimerar utmaningar i produktionsmiljöer.


## <a name="create-a-service-principal-for-azure-ad-in-the-portal"></a>Skapa ett tjänstobjekt för Azure AD i portalen

Om du har distribuerat Azure Stack med Azure AD som Identitetslagret kan skapa du tjänstens huvudnamn precis som du gör för Azure. Den [skapa tjänstens huvudnamn](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-create-service-principals#create-service-principal-for-azure-ad) artikeln visar hur du utför stegen via portalen. Kontrollera att du har den [Azure AD-behörigheter som krävs för](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions) innan du börjar.

## <a name="create-a-service-principal-for-ad-fs-using-powershell"></a>Skapa ett huvudnamn för tjänsten för AD FS med hjälp av PowerShell

Om du har distribuerat Azure Stack med AD FS, kan du använda PowerShell för att skapa ett huvudnamn för tjänsten, tilldela en roll för åtkomst och logga in från PowerShell med hjälp av den identiteten. [Skapa tjänstens huvudnamn för AD FS](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-create-service-principals#create-service-principal-for-ad-fs) visar hur du utför de steg som krävs med hjälp av PowerShell.

## <a name="using-the-azure-stack-api"></a>Med hjälp av Azure Stack API

Den [Azure Stack API](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-rest-api-use) kursen vägleder dig genom processen för att hämta en token för att få åtkomst till Azure Stack-API.

## <a name="connect-to-azure-stack-using-powershell"></a>Ansluta till Azure Stack med hjälp av Powershell

Snabbstarten [att komma igång med PowerShell i Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-configure-quickstart) vägleder dig genom de steg som krävs för att installera Azure PowerShell och ansluta till din Azure Stack-installation.

### <a name="prerequisites"></a>Förutsättningar

En installation av Azure Stack är anslutna till Azure Active Directory med en prenumeration som du kan komma åt. Om du inte har en Azure Stack-installation kan du använda dessa instruktioner för att ställa in en [Azure Stack Development Kit](https://docs.microsoft.com/azure/azure-stack/asdk/asdk-deploy).

#### <a name="connect-to-azure-stack-using-code"></a>Ansluta till Azure Stack med hjälp av kod

Om du vill ansluta till Azure Stack med hjälp av kod använder du Azure Resource Manager-slutpunkter API att hämta autentisering och graph-slutpunkter för Azure Stack-installationen och sedan autentisera med hjälp av REST-begäranden. Du hittar ett exempelklientprogram på [GitHub](https://github.com/shriramnat/HybridARMApplication).

>[!Note]
>Om inte Azure SDK för ditt språkval har stöd för Azure API-profiler, fungerar SDK: N inte med Azure Stack. Läs mer om Azure API-profiler i den [hantera API-versionsprofiler](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-version-profiles) artikeln.

## <a name="next-steps"></a>Nästa steg

 - Läs mer om hur identitet hanteras i Azure Stack i [identitetsarkitektur för Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-identity-architecture).
 - Läs mer om Azure molnmönster i [Molndesignmönster](https://docs.microsoft.com/azure/architecture/patterns).
