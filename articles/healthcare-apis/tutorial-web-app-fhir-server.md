---
title: Självstudie för webb program – konfigurera Azure API för FHIR
description: Den här självstudien vägleder dig genom ett exempel på distribution av ett enkelt webb program. Den här första självstudien beskriver kraven och distributionen av Azure API för FHIR
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: mihansen
ms.author: cavoeg
author: caitlinv39
ms.date: 01/03/2020
ms.openlocfilehash: fcad9356f87e76b14e0ef636389c11fd4290e12a
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2020
ms.locfileid: "84872667"
---
# <a name="deploy-javascript-app-to-read-data-from-fhir-service"></a>Distribuera JavaScript-appen för att läsa data från FHIR-tjänsten
I den här självstudien kommer du att distribuera en liten JavaScript-app som läser data från en FHIR-tjänst. Stegen i den här självstudien är:
1. Distribuera en FHIR-Server
1. Registrera ett offentligt klientprogram
1. Testa åtkomst till programmet
1. Skapa ett webb program som läser dessa FHIR-data

## <a name="prerequisites"></a>Krav
Innan du startar den här uppsättningen med självstudier behöver du följande objekt:
1. En Azure-prenumeration
1. En Azure Active Directory-klientorganisation
1. [Postman](https://www.getpostman.com/) installerat

> [!NOTE]
> I den här självstudien finns FHIR-tjänsten, Azure AD-program och Azure AD-användare i samma Azure AD-klient. Om detta inte är fallet kan du fortfarande följa den här självstudien, men du kan behöva ta del av några av de refererade dokumenten för att utföra ytterligare steg.

## <a name="deploy-azure-api-for-fhir"></a>Distribuera Azure API för FHIR
Det första steget i självstudien är att hämta Azure API för FHIR-installationen korrekt.

1. Distribuera [Azure API för FHIR](fhir-paas-portal-quickstart.md)
1. När du har konfigurerat Azure-API: t för FHIR kan du konfigurera [CORS](configure-cross-origin-resource-sharing.md) -inställningarna genom att gå till ditt Azure-API för FHIR och välja CORS. 
    1. Ange **ursprung** till *
    1. Ange **sidhuvuden** till *
    1. Under **metoder**väljer du **Markera alla**
    1. Ställ in **Maximal ålder** på **600**

## <a name="next-steps"></a>Efterföljande moment
Nu när du har ett Azure-API för FHIR distribuerat, är du redo att registrera ett offentligt klient program.

>[!div class="nextstepaction"]
>[Registrera offentligt klientprogram](tutorial-web-app-public-app-reg.md)
