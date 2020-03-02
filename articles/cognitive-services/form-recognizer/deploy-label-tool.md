---
title: Så här distribuerar du verktyget formulär igenkänning exempel etiketting
titleSuffix: Azure Cognitive Services
description: Lär dig de olika sätt som du kan använda för att distribuera formulär tolkens exempel etikett verktyg som hjälper dig med övervakad inlärning.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: pafarley
ms.openlocfilehash: fa419d7dd9668ac2ce8f2b0eb904117c7e22692d
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/01/2020
ms.locfileid: "78207848"
---
# <a name="deploy-the-sample-labeling-tool"></a>Distribuera verktyget för samplings etiketter

Formulär tolkens exempel etikett verktyg är ett program som körs i en Docker-behållare. Det ger ett användbart användar gränssnitt som du kan använda för att manuellt etikettera formulär dokument i syfte att övervaka inlärningen. Snabb starten för [träna med etiketter](./quickstarts/label-tool.md) visar hur du kör verktyget på din lokala dator, vilket är det vanligaste scenariot. 

Den här guiden beskriver alternativa sätt att distribuera och köra verktyget för etikettering. 

## <a name="deploy-with-azure-container-instances"></a>Distribuera med Azure Container Instances

Du kan köra etikett verktyget i en Docker-webbapp. Börja med att [skapa en ny webb program resurs](https://ms.portal.azure.com/#create/Microsoft.WebSite) på Azure Portal. Fyll i formuläret med din prenumerations-och resurs grupps information. Ange följande information i de obligatoriska fälten:
* **Publicera**: Docker-behållare
* **Operativ** System: Linux

Fyll i följande fält för installationen av Docker-behållaren på nästa sida:

* **Alternativ**: enskild behållare
* **Avbildnings källa**: Azure Container Registry
* **Åtkomst typ**: offentlig
* **Bild och tagg**: MCR.Microsoft.com/Azure-Cognitive-Services/Custom-form/labeltool:Latest

De steg som följer är valfria. När appen har distribuerats kan du köra den och komma åt etikett verktyget online.

### <a name="connect-to-azure-ad-for-authorization"></a>Ansluta till Azure AD för auktorisering

Vi rekommenderar att du ansluter din webbapp till Azure Active Director (AAD) så att bara någon med dina autentiseringsuppgifter kan logga in och använda appen. Följ anvisningarna i [Konfigurera din app service-app](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-aad) för att ansluta till AAD.

## <a name="next-steps"></a>Nästa steg

Gå tillbaka till [träna med](./quickstarts/label-tool.md) snabb start av etiketter och lär dig hur du använder verktyget för att manuellt etikettera tränings data och göra övervakad inlärning.
