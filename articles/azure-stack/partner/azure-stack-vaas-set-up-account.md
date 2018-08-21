---
title: Ställ in din Azure Stack-validering som ett tjänstkonto | Microsoft Docs
description: Lär dig hur du ställer in din verifiering som ett tjänstkonto.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 07/24/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: f6c78a3e79ac88194e7e34ad8be7a941ee715d39
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2018
ms.locfileid: "40235491"
---
# <a name="set-up-your-validation-as-a-service-account"></a>Ställ in din validering som ett tjänstkonto

[!INCLUDE[Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Verifiering som en tjänst (VaaS) är en Azure-tjänst som är tillgänglig för Microsoft Azure Stack-partner som har en delad engineering avtal med Microsoft för att utforma, utveckla, verifiera, sälja, distribuera och stöd för Azure Stack-lösningar på marknaden.

Lär dig hur du förbereder datorn för verifiering som tjänst. Konfigurera Azure Active Directory-instans och utföra andra uppgifter som krävs för att komma igång med VaaS. 

Aktiviteterna är:

- Skapa ett Azure Storage-blob för att lagra loggar
- Distribuera lokal agent
- Hämta virtuella datorer för testning avbildningen på Azure Stack-instans som ska testas

## <a name="create-an-azure-active-directory-tenant-id"></a>Skapa ett Azure Active Directory-klient-ID

1. Skapa en Azure Active Directory-klient i den [Azure-portalen](https://portal.azure.com) eller Använd en befintlig klient.

    Rekommenderar vi att du skapar en klient som ska användas med VaaS med ett beskrivande namn, t.ex, ContosoVaaS@onmicrosoft.com. Rollbaserad åtkomstkontroll (RBAC) funktioner för klienten används av partnern för att hantera vilka i partnerorganisationen kan använda VaaS.  
    
    Mer information finns i [hantera Azure AD-katalogen](https://docs.microsoft.com/azure/active-directory/active-directory-administer).

    > [!Note]  
    > Mer information om hur du skapar nya Azure Active Directory-klienter finns i [Kom igång med Azure AD](https://docs.microsoft.com/azure/active-directory/get-started-azure-ad).

2. Lägg till medlemmar i din organisation som ansvarar för att använda tjänsten för klienten. Tilldela varje användare i klienten något av följande roller att styra sina åtkomstnivå till VaaS:

    | Rollnamn | Beskrivning |
    |---------------------|------------------------------------------|
    | Ägare | Har fullständig åtkomst till alla resurser. |
    | Läsare | Kan visa alla resurser, men inte redigera. |
    | Test-deltagare | Hantera test-resurser. |
    | Katalogen deltagare | Kan hantera Publiceringsresurser för lösningen. |

## <a name="set-up-your-tenant"></a>Konfigurera din klient

Konfigurera din klient i den **Azure Stack-verifiering Service** program. 

1. Skicka följande information om klienten till Microsoft på vaashelp@microsoft.com.

    | Data | Beskrivning |
    |--------------------------------|---------------------------------------------------------------------------------------------|
    | Organisationens namn | Officiella organisationsnamn. |
    | Katalognamnet för Azure AD-klient | Azure AD-klient katalognamn som registreras. |
    | Katalog-Id för Azure AD-klient | Azure AD-klient Directory GUID som är kopplade till katalogen.<br> Information om hur du hittar din katalog-ID för Azure AD-klient finns, finns i ”[hämta klient-ID](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#get-tenant-id)”. |

    

2. Azure Stack-teamet innehåller bekräftelse att din klient kan använda VaaS-portalen.

3. Använd de autentiseringsuppgifter som global administratör för klienten för att logga in på den [VaaS portal](https://azurestackvalidation.com/
). Välj **mitt konto**.

    ![Logga VaaS-portalen](media/vaas_portalsignin.png)

3. Webbplatsen uppmanas du att ha åtkomst till VaaS. Acceptera licensvillkoren om du vill fortsätta.

## <a name="assign-user-roles"></a>Tilldela användarroller

Så här tilldelar du en användarroll:

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj **alla tjänster** > **Azure Active Directory** i den **identitet** grupp.
3. Välj **företagsprogram** > **Azure Stack-verifiering Service** program.
4. Välj **Användare och grupper**. Den **Azure Stack-verifiering Service - användare och grupper** bladet visar en lista över användare med behörighet att använda programmet.
5. Välj **+ Lägg till användare** att lägga till en uppgift.

## <a name="create-an-azure-storage-blob-to-store-logs"></a>Skapa en Azure storage-blob för att lagra loggar

VaaS skapar diagnostikloggar när du kör verifieringstesterna. Du behöver en plats till en Azure blob-tjänst SAS-Webbadressen för att lagra dina loggar. Storage-konto kan också användas till store OEM-anpassning paket.

Dessa steg beskriver hur du ställer in och generera en storage as a service (SAS)-URI för ett Azure storage-konto och var du vill ange storage-konto i portalen VaaS när du startar testerna i portalen.

### <a name="create-an-azure-storage-account"></a>Skapa ett Azure-lagringskonto

1. Om du vill skapa ett lagringskonto i anvisningarna [skapa ett lagringskonto](https://docs.microsoft.com/azure/storage/storage-create-storage-account#create-a-storage-account).

2. När du väljer typ av lagringskonto, Välj den **Blob-lagring** kontotyp.

### <a name="generate-a-sas-url-for-the-storage-account"></a>Generera en SAS-URL för storage-konto

1. Gå till det lagringskonto som skapades ovan.

2. På bladet under **inställningar**väljer **signatur för delad åtkomst**.

3. Kontrollera endast **Blob** från **tillåtna tjänster alternativ** (avmarkera de återstående).

4. Kontrollera **Service**, ** behållare och **objekt** från **tillåtna resurstyper**.

5. Kontrollera **Läs**, **skriva**, **lista**, **Lägg till**, **skapa** från **tillåtna behörigheter**  (avmarkera de återstående).

6. Ange **starttid** till aktuell tid och **sluttid** till tre månader från den aktuella tiden.

7. Välj **generera SAS och anslutningssträng** och spara den **Blob service SAS-Webbadressen** sträng.

> [!Note]  
> SAS-Webbadressen upphör att gälla den sluttid som anges när URL: en har genererats. Kontrollera att URL: en är tillräckligt giltig innan den delas med produktteam för felsökning eller att URL: en är giltig i mer än 30 dagar när du schemalägger tester.

## <a name="next-steps"></a>Nästa steg

- Använd VaaS lokal agent för att kontrollera om maskinvaran. Anvisningar finns i [distribuera de lokala virtuella datorerna för agenten och testa](azure-stack-vaas-test-vm.md).
- Mer information om [Azure Stack-verifiering som en tjänst](https://docs.microsoft.com/azure/azure-stack/partner).