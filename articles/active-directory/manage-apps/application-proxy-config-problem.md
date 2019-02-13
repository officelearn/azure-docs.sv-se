---
title: Problem med att skapa ett program med Application Proxy | Microsoft Docs
description: Så här felsöker du problem med att skapa Application Proxy-program på Azure AD-administrationsportalen
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
ms.openlocfilehash: e7aad73331d06828a4bfe909dbb3b98350b1d0d6
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56166660"
---
# <a name="problem-creating-an-application-proxy-application"></a>Problem med att skapa ett programproxy-program 

Nedan visas några av de vanligaste problemen som personer ansikte när du skapar ett nytt application proxy-program.

## <a name="recommended-documents"></a>Rekommenderade dokument 

Mer information om hur du skapar ett programproxy-program via administrationsportalen finns [publicera program med Azure AD Application Proxy](application-proxy-add-on-premises-application.md).

Om du följer stegen i dokumentet och får ett fel när programmet se felinformationen för information och förslag om hur du löser programmet. De vanligaste felmeddelanden som innehåller en föreslagen åtgärd. 

## <a name="specific-things-to-check"></a>Specifika saker att kontrollera

Kontrollera om du vill undvika vanliga fel:

-   Du är administratör med behörighet att skapa en Application Proxy-program

-   Den interna URL: en är unikt

-   Den externa URL: en är unikt

-   URL: er börjar med http eller https och sluta med en ”/”

-   URL: en ska vara ett domännamn och inte en IP-adress

Ett felmeddelande visas i det övre högra hörnet när du skapar programmet. Du kan också välja meddelandeikonen till felmeddelanden visas.

   ![Fråga om meddelande](./media/application-proxy-config-problem/error-message.png)

## <a name="next-steps"></a>Nästa steg
[Aktivera Application Proxy i Azure portal](application-proxy-add-on-premises-application.md)
