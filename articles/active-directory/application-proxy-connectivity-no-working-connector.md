---
title: Hitta någon fungerande anslutningsgrupp för ett program med Application Proxy | Microsoft Docs
description: Åtgärda problem som kan uppstå när det finns ingen fungerande anslutning i en Anslutningsgrupp för ditt program med Azure AD-programproxyn
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: 82014e1eaadc343ac320b0f4b3091d530228a8c5
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2018
ms.locfileid: "39366169"
---
# <a name="no-working-connector-group-found-for-an-application-proxy-application"></a>Ingen fungerande anslutningsgrupp hittades för ett Application Proxy-program

Den här artikeln hjälper dig för att lösa vanliga problem som kan stöta på när det inte är en anslutning som har identifierats för ett Application Proxy-program som är integrerad med Azure Active Directory.

## <a name="overview-of-steps"></a>Översikt över stegen
Om det finns ingen fungerande anslutning i en Anslutningsgrupp för ditt program, finns det ett antal sätt att lösa problemet:

-   Om du har inga anslutningar i gruppen kan du:

    -   Hämta en ny Anslutningsapp på rätt på lokala servrar och tilldela den till den här gruppen

    -   Flytta en aktiv anslutning till gruppen

-   Om du har inga aktiva anslutningar i gruppen kan du:

    -   Identifiera orsaken till Anslutningsappen är inaktiv och lösa

    -   Flytta en aktiv anslutning till gruppen

Öppna menyn ”Application Proxy” i ditt program för att lösa problemet och titta på Anslutningsgruppen varningsmeddelandet. Om det finns inga anslutningar i gruppen, anger varningsmeddelandet gruppen måste minst en koppling. Om du har inga aktiva anslutningar, förklarar som i varningsmeddelandet. Det är vanligt att ha inaktiva kopplingar. 

   ![Val av anslutningen i Azure-portalen](./media/application-proxy-connectivity-no-working-connector/no-active-connector.png)

Information om vart och ett av dessa alternativ finns i motsvarande avsnitt nedan. Anvisningarna förutsätter att du startar från sidan för hantering av anslutningen. Om du tittar på felmeddelandet ovan, går du till den här sidan genom att klicka på varningsmeddelandet. Du kan också öppna sidan genom att gå till **Azure Active Directory**, klicka på **företagsprogram**, sedan **Application Proxy.**

   ![Connector grupphantering i Azure-portalen](./media/application-proxy-connectivity-no-working-connector/app-proxy.png)

## <a name="download-a-new-connector"></a>Ladda ned en ny anslutning

Använd knappen ”Hämta Connector” överst på sidan om du vill hämta en ny anslutning.

Installera anslutningen på en dator med direkt åtkomst till backend-applikationer. Anslutningen är vanligtvis installerad på samma server som programmet. När du hämtat, bör anslutningen visas i den här menyn. Klicka på anslutningen och använda ”Anslutningsgruppen” listrutan för att se till att det hör till rätt grupp. Spara ändringen.

   ![Hämta connector från Azure-portalen](./media/application-proxy-connectivity-no-working-connector/download-connector.png)
   
## <a name="move-an-active-connector"></a>Flytta en aktiv anslutning

Om du har en aktiv anslutning som ska tillhöra gruppen och har åtkomst till målprogrammet för serverdelen kan flytta du anslutningen till den tilldelade gruppen. Du gör detta genom att klicka på kopplingen. I fältet ”Anslutningsgrupp” använder du listrutan att välja rätt grupp Klicka på Spara.

## <a name="resolve-an-inactive-connector"></a>Lösa en inaktiv anslutning

Om de enda kopplingarna i gruppen är inaktiva, sannolikt de på en dator som inte har de nödvändiga portarna som avblockerad.

Se portar Felsök dokument för mer information på undersöka problemet.

## <a name="next-steps"></a>Nästa steg
[Förstå Azure AD Application Proxy-anslutningar](manage-apps/application-proxy-connectors.md)


