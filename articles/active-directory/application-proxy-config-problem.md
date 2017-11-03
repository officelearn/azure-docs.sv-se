---
title: Skapa ett program med Application Proxy | Microsoft Docs
description: "Så här felsöker du problem med att skapa programproxy program i Azure AD Admin portal"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: fe56f56162ba7186f1b660a5b44fcef38f1dee9d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="problem-creating-an-application-proxy-application"></a>Skapa ett program med Application Proxy 

Nedan visas några vanliga problem personer står inför när du skapar ett nytt program för application proxy.

## <a name="recommended-documents"></a>Rekommenderade dokument 

Mer information om hur du skapar ett program för Application Proxy via Admin Portal finns [publicera program med Azure AD Application Proxy](https://docs.microsoft.com/azure/active-directory/application-proxy-publish-azure-portal).

Om du följer stegen i dokumentet och får ett fel när programmet finns i felinformationen och förslag om hur du löser programmet. De vanligaste felmeddelanden som innehåller en föreslagen åtgärd. 

## <a name="specific-things-to-check"></a>Vissa saker att kontrollera

Kontrollera följande för att undvika vanliga fel:

-   Du är administratör med behörighet att skapa ett program med Application Proxy

-   Intern URL är unikt

-   Den externa URL: en är unikt

-   URL: er börja med http eller https och sluta med en ”/”

-   URL-Adressen ska vara ett domännamn och inte en IP-adress

Felmeddelandet ska visas i det övre högra hörnet när du skapar i program. Du kan också välja meddelandeikonen att se felmeddelanden.

   ![Meddelande-fråga](./media/application-proxy-config-problem/error-message.png)

## <a name="next-steps"></a>Nästa steg
[Aktivera Application Proxy på Azure-portalen](active-directory-application-proxy-enable.md)
