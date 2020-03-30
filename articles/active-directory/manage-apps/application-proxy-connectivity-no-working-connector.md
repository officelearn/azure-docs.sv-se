---
title: Ingen arbetsgrupp hittades för en programproxyapp
description: Åtgärda problem som kan uppstå när det inte finns någon fungerande anslutningsapp i en anslutningsgrupp för ditt program med Azure AD Application Proxy
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 96ab0d1b3bbab9c97c04da4f918f3aaa2f1d07e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74275624"
---
# <a name="no-working-connector-group-found-for-an-application-proxy-application"></a>Ingen arbetsgrupp hittades för ett programproxyprogram

Den här artikeln hjälper till att lösa de vanliga problem som uppkom när det inte finns en anslutning som har identifierats för ett programproxy-program integrerat med Azure Active Directory.

## <a name="overview-of-steps"></a>Översikt över steg
Om det inte finns någon fungerande kopplingsapp i en anslutningsgrupp för ditt program finns det några sätt att lösa problemet:

-   Om du inte har några kopplingar i gruppen kan du:

    -   Hämta en ny anslutningsapp till höger på den lokala servern och tilldela den till den här gruppen

    -   Flytta en aktiv koppling till gruppen

-   Om du inte har några aktiva kopplingar i gruppen kan du:

    -   Identifiera orsaken till att kopplingen är inaktiv och lösa

    -   Flytta en aktiv koppling till gruppen

Om du vill ta reda på problemet öppnar du menyn "Programproxy" i ditt program och tittar på varningsmeddelandet för Anslutningsgruppen. Om det inte finns några kopplingar i gruppen anger varningsmeddelandet att gruppen behöver minst en koppling. Om du inte har några aktiva kopplingar förklaras det i varningsmeddelandet. Det är vanligt att ha inaktiva kopplingar. 

   ![Val av kopplingsgrupp i Azure-portalen](./media/application-proxy-connectivity-no-working-connector/no-active-connector.png)

Mer information om vart och ett av dessa alternativ finns i motsvarande avsnitt nedan. Instruktionerna förutsätter att du startar från sidan Hantering av anslutning. Om du tittar på felmeddelandet ovan kan du gå till den här sidan genom att klicka på varningsmeddelandet. Du kan också komma till sidan genom att gå till **Azure Active Directory**, klicka på Enterprise **Applications**och sedan **Programproxy.**

   ![Hantering av Anslutningsgrupp i Azure-portalen](./media/application-proxy-connectivity-no-working-connector/app-proxy.png)

## <a name="download-a-new-connector"></a>Ladda ner en ny connector

Om du vill hämta en ny Connector använder du knappen "Ladda ned anslutningsappen" högst upp på sidan.

Installera kontakten på en maskin med direkt siktlinje till backend-programmet. Vanligtvis installeras anslutningen på samma server som programmet. När du har hämtat ska Kopplingen visas på den här menyn. Klicka på kopplingskontakten och använd listrutan "Kopplingsgrupp" för att kontrollera att den tillhör rätt grupp. Spara ändringen.

   ![Hämta kopplingen från Azure-portalen](./media/application-proxy-connectivity-no-working-connector/download-connector.png)
   
## <a name="move-an-active-connector"></a>Flytta en aktiv koppling

Om du har en aktiv koppling som ska tillhöra gruppen och har siktlinje till målserverdelsprogrammet kan du flytta kopplingen till den tilldelade gruppen. Det gör du genom att klicka på kopplingen. I fältet "Kopplingsgrupp" använder du listrutan för att markera rätt grupp och klickar på Spara.

## <a name="resolve-an-inactive-connector"></a>Lösa en inaktiv koppling

Om de enda kopplingarna i gruppen är inaktiva är de troligen på en dator som inte har alla nödvändiga portar avblockerade.

Se portarna Felsöka dokument för information om hur du undersöker problemet.

## <a name="next-steps"></a>Nästa steg
[Förstå Azure AD-programproxy-kopplingar](application-proxy-connectors.md)


