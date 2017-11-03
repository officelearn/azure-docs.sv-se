---
title: Certifiera din anpassade kopplingar - Azure Logic Apps | Microsoft Docs
description: "Göra kopplingar tillgängliga för alla användare i Azure Logic Apps och Microsoft Flow Microsoft PowerApps"
author: ecfan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: LADocs; estfan
ms.openlocfilehash: 9bcb138c181df0c12548cc5dc721542fd9c9ba68
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="submit-your-connectors-for-microsoft-certification"></a>Skicka dina kontakter för Microsoft-certifikat

Att göra egna kopplingar offentligt tillgängliga för alla användare i Logikappar i Azure, Microsoft Flow och Microsoft PowerApps skicka din anslutning till Microsoft för granskning, verifiering och godkännande för publicering. 

## <a name="certification-criteria"></a>Villkor för certifikatutfärdare

| Funktion | Information | Nödvändiga eller rekommenderade |
|------------|---------|-------------------------|
| Programvara som en-tjänst (SaaS)-app | Uppfyller Användarscenario som passar bra med Logic Apps, flöde och PowerApps. | Krävs |
| Autentiseringstyp | Din API måste stödja OAuth2, API-nyckel eller grundläggande autentisering. | Krävs | 
| Support | Du måste ange en supportkontakten så att kunder kan få hjälp. | Krävs | 
| Tillgänglighet och drifttid | Appen har minst 99,9% drifttid. | Rekommenderas | 
|||| 

Även om du inte är en Microsoft-partner eller oberoende leverantör (ISV) och du vill att certifiera och offentligt frigöra en koppling, måste du antingen äga den underliggande tjänsten eller finns explicit behörighet för att använda API: et.

## <a name="validation-phases"></a>Verifieringen faser

Microsoft använder faserna verifiering för att utvärdera din koppling:

| Valideringsfasen | Beskrivning | 
| ----- | ----------- |
| Funktioner | Microsoft testar din anslutning med Logic Apps, flödesreglering och PowerApps för dessa fel: <p>-Ogiltig OpenAPI (Swagger) eller JSON fel som visas i avsnittet Definition från guiden Anpassad koppling <p>-Runtime och schema valideringsfel som visas i avsnittet Test från guiden Anpassad koppling | 
| Innehåll | Microsoft kontrollerar du att din anslutningen använder egna namn och beskrivningar för varje entitet. Se till att varje åtgärd och Indataparametern svar attribut i din anslutningstjänst Swagger har dessa element: <p>- [Sammanfattning](../logic-apps/custom-connector-openapi-extensions.md#summary) <br>- [Beskrivning](../logic-apps/custom-connector-openapi-extensions.md#description) </br>- [Synlighet information](../logic-apps/custom-connector-openapi-extensions.md#visibility) | 
||| 

## <a name="nominate-and-submit-your-connector-to-microsoft-for-certification"></a>Utse och skicka din anslutning till Microsoft för certifikatutfärdare

Följ dessa steg om du vill använda för certifiering:

1. **Utse**

   1. [Skicka din kandidat](https://go.microsoft.com/fwlink/?linkid=848754).

      1. Längst ned på sidan Välj **kontaktar du oss**.
      2. Fylla i formuläret och välj **frågor om ISV-koppling program och samtidigt marknadsföring**.
      3. Välj **skicka**.

   2. Signera ömsesidig sekretessavtal och partneravtalet som visas. 

      Microsoft kräver dessa signerade kontrakt innan du fortsätter. 
      Vi kan sedan kontrollera om din anslutningstjänst uppfyller villkoren för certifikatutfärdare. 
   
   3. Om din anslutningstjänsten godkänns meddelar Microsoft dig med instruktioner för onboarding.
    
2. **Granska**

   1. Skicka denna information till kontakten kandidat för granskning:

      * OpenAPI-fil som beskriver ditt API
      * Den ikonfil (.png eller .jpg) som representerar din kontakt 
      
        En ikon ska ha en ~ 160 pixel logotyp i en ruta 230 pixel. 
        En vit logotyp på en bakgrundsfärg är att föredra.
      
      * En ikon varumärken färg i hexadecimalt format som ska matcha bakgrundsfärg i ikonfilen

      * Ett testkonto för verifiering
      * Support-kontakta

   2. Om vi behöver mer information kan kontakta vi dig.

3. **Publicera**

    När vi verifiera din anslutningstjänst funktioner och innehåll mellanlagra vi kopplingen för distribution på alla produkter och regioner.
    
    Som standard publiceras alla kopplingar som ”premium-kopplingar. 
    Om du har skapat din app med Azure som du kan använda för att lista din koppling som ”standard” koppling som är tillgängliga för alla användare med Office 365 Enterprise planer. 
    Be kontakten kandidat för mer information.

## <a name="next-steps"></a>Nästa steg

* [Anpassad koppling vanliga frågor och svar](../logic-apps/custom-connector-faq.md)
* [Översikt över anpassad koppling](../logic-apps/custom-connector-overview.md)