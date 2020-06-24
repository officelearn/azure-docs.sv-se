---
title: 'Snabb start: Distribuera Azure API för FHIR med hjälp av Azure Portal'
description: I den här snabb starten får du lära dig hur du distribuerar Azure API för FHIR och konfigurerar inställningar med hjälp av Azure Portal.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: e729597e9d83c4e6096fe52b577b052d94ca4799
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "84820254"
---
# <a name="quickstart-deploy-azure-api-for-fhir-using-azure-portal"></a>Snabb start: Distribuera Azure API för FHIR med hjälp av Azure Portal

I den här snabb starten får du lära dig hur du distribuerar Azure API för FHIR med hjälp av Azure Portal.

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="create-new-resource"></a>Skapa ny resurs

Öppna [Azure Portal](https://portal.azure.com) och klicka på **skapa en resurs**

![Skapa en resurs](media/quickstart-paas-portal/portal-create-resource.png)

## <a name="search-for-azure-api-for-fhir"></a>Sök efter Azure API för FHIR

Du kan hitta Azure API för FHIR genom att skriva "FHIR" i sökrutan:

![Sök efter hälso vårds-API: er](media/quickstart-paas-portal/portal-search-healthcare-apis.png)

## <a name="create-azure-api-for-fhir-account"></a>Skapa Azure API för FHIR-konto

Välj **skapa** för att skapa ett nytt Azure API för FHIR-konto:

![Skapa Azure API för FHIR-konto](media/quickstart-paas-portal/portal-create-healthcare-apis.png)

## <a name="enter-account-details"></a>Ange konto information

Välj en befintlig resurs grupp eller skapa en ny, Välj ett namn för kontot och klicka slutligen på **Granska + skapa**:

![Ny hälso-API-information](media/quickstart-paas-portal/portal-new-healthcareapi-details.png)

Bekräfta skapandet och vänta på FHIR API-distribution.

## <a name="additional-settings"></a>Ytterligare inställningar

Klicka på **Nästa: ytterligare inställningar** för att konfigurera utfärdare, mål grupp, identitets objekt-ID: n som ska kunna få åtkomst till det här Azure-API: t för FHIR, aktivera Smart på FHIR vid behov och konfigurera databas data flöde:

- **Utfärdare:** Du kan ange en annan Azure AD-klient än den som du är inloggad på som autentiseringsmetod för tjänsten.
- **Mål grupp:** Bästa praxis och Standardinställningen är att mål gruppen har angetts till URL: en för FHIR-servern. Du kan ändra det här. Mål gruppen identifierar mottagaren som token är avsedd för. I det här sammanhanget ska det vara inställt på något som representerar själva FHIR-API: et.
- **Tillåtna objekt-ID:** Du kan ange identitets objekt-ID: n som ska få åtkomst till det här Azure-API: t för FHIR. Du kan lära dig mer om att hitta objekt-ID: t för användare och tjänst huvud namn i instruktionen [hitta identitets objekt-ID: n](find-identity-object-ids.md) .  
- **Smart on FHIR-proxy:** Du kan aktivera SMART on FHIR proxy. Mer information om hur du konfigurerar SMART på FHIR proxy finns i självstudie [Azure API for FHIR Smart on FHIR proxy](https://docs.microsoft.com/azure/healthcare-apis/use-smart-on-fhir-proxy)  
- **Allokerat data flöde (ru/s):** Här kan du ange inställningar för data flöde för den underliggande databasen för ditt Azure API för FHIR. Du kan ändra den här inställningen senare i databas bladet. Mer information finns på sidan [Konfigurera databas inställningar](configure-database.md) .


![Konfigurera tillåtna objekt-ID: n](media/quickstart-paas-portal/configure-audience.png)

## <a name="fetch-fhir-api-capability-statement"></a>Hämta FHIR API Capability-instruktion

Om du vill kontrol lera att det nya FHIR API-kontot har tillhandahållits, hämtar du en kapacitets instruktion genom att peka en webbläsare till `https://<ACCOUNT-NAME>.azurehealthcareapis.com/metadata` .

## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs kan du ta bort resurs gruppen, Azure API för FHIR och alla relaterade resurser. Om du vill göra det väljer du den resurs grupp som innehåller Azure API för FHIR-kontot, väljer **ta bort resurs grupp**och bekräftar sedan namnet på den resurs grupp som ska tas bort.

## <a name="next-steps"></a>Nästa steg

I den här snabb starts guiden har du distribuerat Azure API för FHIR i din prenumeration. Om du vill ange ytterligare inställningar i Azure-API: et för FHIR går du vidare till ytterligare inställningar instruktions guide.

>[!div class="nextstepaction"]
>[Ytterligare inställningar i Azure API för FHIR](azure-api-for-fhir-additional-settings.md)
