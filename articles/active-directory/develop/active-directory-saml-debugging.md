---
title: "Felsöka SAML-baserade enkel inloggning till program i Azure Active Directory | Microsoft Docs"
description: "Lär dig att felsöka SAML-baserade enkel inloggning till program i Azure Active Directory "
services: active-directory
author: asmalser-msft
documentationcenter: na
manager: femila
ms.assetid: edbe492b-1050-4fca-a48a-d1fa97d47815
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/20/2017
ms.author: asmalser
ms.custom: aaddev
ms.reviewer: dastrock
ms.openlocfilehash: 31447d597296bac57481dc2acb4a95ee3a104161
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2017
---
# <a name="how-to-debug-saml-based-single-sign-on-to-applications-in-azure-active-directory"></a>Felsöka SAML-baserade enkel inloggning till program i Azure Active Directory
När du felsöker en SAML-baserade programintegration, är det ofta praktiskt att använda ett verktyg som [Fiddler](http://www.telerik.com/fiddler) att se SAML-begäran, SAML-svar och faktiska SAML-token som utfärdas till programmet. Genom att undersöka SAML-token kan du se till att alla obligatoriska attribut, användarnamnet i SAML-ämne och utfärdaren URI kommer som förväntat.

![][1]

Svaret från Azure AD som innehåller SAML-token är vanligtvis det som sker när en HTTP 302-omdirigering från https://login.windows.net och skickas till den konfigurerade **Reply URL** av programmet. 

Du kan visa SAML-token genom att välja den här raden och sedan välja den **kontrollanter > WebForms** fliken i den högra panelen. Därifrån kan du högerklicka på den **SAMLResponse** värdet och välj **skicka till TextWizard**. Välj sedan **från Base64** från den **transformera** menyn för att avkoda token och se innehållet.

**Obs**: Om du vill visa innehållet i den här HTTP-begäran, Fiddler kan du uppmanas att konfigurera dekryptering av HTTPS-trafik som du behöver göra.

## <a name="related-articles"></a>Relaterade artiklar
* [Artikelindex för programhantering i Azure Active Directory](../active-directory-apps-index.md)
* [Konfigurera enkel inloggning för program som inte ingår i Azure Active Directory-programgalleriet](../active-directory-saas-custom-apps.md)
* [Anpassa anspråk som utfärdats i SAML-Token för förintegrerade appar](active-directory-saml-claims-customization.md)

<!--Image references-->
[1]: ../media/active-directory-saml-debugging/fiddler.png