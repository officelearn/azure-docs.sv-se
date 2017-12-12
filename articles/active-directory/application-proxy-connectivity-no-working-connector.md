---
title: "Ingen koppling arbetsgruppen hittades för ett program med Application Proxy | Microsoft Docs"
description: "Åtgärda problem som kan uppstå när det finns ingen aktiv anslutning i en grupp för anslutningen för ditt program med Azure AD Application Proxy"
services: active-directory
documentationcenter: 
author: ajamess
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 7aad43be4dc411215a4b54c21a16c4cbb949c7ec
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="no-working-connector-group-found-for-an-application-proxy-application"></a>Ingen koppling arbetsgruppen hittades för ett program med Application Proxy

Den här artikeln hjälp dig att lösa vanliga problem inför när det inte är en koppling upptäcktes för ett program med Application Proxy integrerat med Azure Active Directory.

## <a name="overview-of-steps"></a>Översikt över steg
Om det finns ingen aktiv anslutning i en grupp för kopplingen för programmet, finns det några sätt att lösa problemet:

-   Om du har inga kopplingar i gruppen kan du:

    -   Hämta en ny koppling på rätt lokal server och tilldela den här gruppen

    -   Flytta en aktiv koppling till gruppen

-   Om du har inga aktiva kopplingar i gruppen kan du:

    -   Identifiera orsaken din anslutning är inaktiv och lösa

    -   Flytta en aktiv koppling till gruppen

Om du vill veta vilka av dessa är problemet, öppna menyn ”Application Proxy” i ditt program och titta på varningsmeddelandet Connector grupp. Det anger att gruppen måste minst en koppling (du har ingen i gruppen) eller att det finns inga aktiva anslutningar (även om du har förmodligen inaktiva kopplingar).

   ![Val av anslutningen i Azure Portal](./media/application-proxy-connectivity-no-working-connector/no-active-connector.png)

Mer information om de här alternativen finns i avsnittet motsvarande nedan. Var och en av dessa förutsätter att du startar från sidan för hantering av anslutningen. Om du tittar på felmeddelandet går du till den här sidan genom att klicka på varningsmeddelandet. Annars det hittar du genom att gå till **Azure Active Directory**, klicka på **företagsprogram**, sedan **Application Proxy.**

   ![Kopplingen grupphantering i Azure Portal](./media/application-proxy-connectivity-no-working-connector/app-proxy.png)

## <a name="download-a-new-connector"></a>Hämta en ny koppling

Använd knappen ”Hämta anslutning” längst upp på sidan om du vill hämta en ny koppling.

Observera anslutningen måste vara installerad på en dator med fri till backend-programmet och placeras vanligtvis på samma server som programmet. När du har hämtat, ska kopplingen visas i den här menyn. Klicka på anslutningen och använda listrutan ”Connector grupp” för att se till att det tillhör gruppen rätt. Spara ändringen.

   ![Ladda ned anslutningen från Azure-portalen](./media/application-proxy-connectivity-no-working-connector/download-connector.png)
   
## <a name="move-an-active-connector"></a>Flytta en aktiv koppling

Om du har en aktiv koppling som ska tillhöra gruppen och har fri backend målprogrammet kan du flytta kopplingen till grupp. Gör du genom att klicka på kopplingen. I fältet ”Connector grupp” använder du i listrutan och välj rätt grupp klickar du på Spara.

## <a name="resolve-an-inactive-connector"></a>Lösa en inaktiv anslutning

Om endast kopplingar i gruppen är inaktiva sannolikt de på en dator som inte har de nödvändiga portarna som avblockerad.

Se portar felsöka dokument för mer information på undersöker problemet.

## <a name="next-steps"></a>Nästa steg
[Förstå Azure AD Application Proxy-kopplingar](application-proxy-understand-connectors.md)


