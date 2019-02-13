---
title: Hitta någon fungerande anslutningsgrupp för ett program med Application Proxy | Microsoft Docs
description: Åtgärda problem som kan uppstå när det finns ingen fungerande anslutning i en Anslutningsgrupp för ditt program med Azure AD-programproxyn
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: celested
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6004feaa4b720b1745fbbbeaab1cddb0a46a7498
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56170626"
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
[Förstå Azure AD Application Proxy-anslutningar](application-proxy-connectors.md)


