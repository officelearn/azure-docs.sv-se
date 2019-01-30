---
title: Azure Stack store autentiseringsuppgifter för tjänstens huvudnamn i Key Vault | Microsoft Docs
description: Lär dig hur Key Vault lagrar autentiseringsuppgifter för tjänstens huvudnamn i Azure Stack
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2019
ms.author: sethm
ms.lastreviewed: 01/16/2019
ms.openlocfilehash: d4bc560a1c607d4a12cfb3d08ffeacf9b5d2196a
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2019
ms.locfileid: "55244291"
---
# <a name="store-service-principal-credentials-in-key-vault"></a>Store autentiseringsuppgifter för tjänstens huvudnamn i Key Vault

Programutveckling i Azure Stack normalt kräver skapar ett tjänstens huvudnamn och använda dessa autentiseringsuppgifter för att autentisera innan du distribuerar. Dock är ofta de lagrade autentiseringsuppgifterna för tjänstens huvudnamn felplacerad. Den här artikeln beskriver hur du skapa tjänstens huvudnamn och lagra värdena i Azure Key Vault för senare hämtning.

Läs mer om Key Vault, [i den här artikeln](azure-stack-key-vault-intro.md).

## <a name="prerequisites"></a>Förutsättningar

- En prenumeration på ett erbjudande som innehåller Azure Key Vault-tjänsten.
- PowerShell har konfigurerats för användning med Azure Stack.

## <a name="key-vault-in-azure-stack"></a>Nyckelvalv i Azure Stack

Key Vault i Azure Stack hjälper dig för att skydda kryptografiska nycklar och hemligheter som program och tjänster i molnet använder. Med Key Vault kan kryptera du nycklar och hemligheter.

Följ dessa steg om du vill skapa ett nyckelvalv:

1. Logga in på Azure Stack-portalen.

2. Från instrumentpanelen väljer **+ skapa en resurs**, sedan **säkerhet + identitet**och välj sedan **Key Vault.**

   ![Skapa nyckelvalv](media/azure-stack-key-vault-store-credentials/create-key-vault.png)

3. I den **skapa Nyckelvalv** fönstret tilldela en **namn** för ditt valv. Vault-namn får innehålla endast alfanumeriska tecken och bindestreck (-). De får inte börja med en siffra.

4. Välj en prenumeration i listan med tillgängliga prenumerationer.

5. Välj en befintlig resursgrupp eller skapa en ny.

6. Välj prisnivå.

7. Välj något av de befintliga åtkomstprinciperna eller skapa en ny. En åtkomstprincip kan du bevilja behörigheter för en användare, program eller en säkerhetsgrupp att utföra åtgärder med det här valvet.

8. Du kan också välja en avancerad åtkomstprincip för att aktivera åtkomst till funktioner.

9. När du har konfigurerat inställningarna, Välj **OK**, och välj sedan **skapa**. Nyckelvalv-distributionen börjar.

## <a name="create-a-service-principal"></a>Skapa ett huvudnamn för tjänsten

1. Logga in på ditt Azure-konto via Azure portal.

2. Välj **Azure Active Directory**, sedan **appregistreringar**, sedan **Lägg till**.

3. Ange ett namn och en URL för programmet. Välj antingen **webbapp / API** eller **interna** för typ av program som du vill skapa. När du har angett värdena, Välj **skapa**.

4. Välj **Active Directory**, sedan **Appregistreringar**, och välj ditt program.

5. Kopiera **Program-ID:t** och lagra det i din programkod. Använda program i exempelprogrammen **klient-ID** när det gäller den **program-ID**.

6. Välj **Nycklar** om du vill generera en autentiseringsnyckel.

7. Ange en beskrivning för nyckeln och en varaktighet.

8. Välj **Spara**.

9. Kopiera den **nyckel** som blir tillgänglig när du klickat på **spara**.

## <a name="store-the-service-principal-inside-key-vault"></a>Store tjänstens huvudnamn i Key Vault

1. Logga in på användarportalen för Azure Stack, och välj det nyckelvalv som du skapade tidigare och välj sedan den **hemlighet** panelen.

2. I den **hemlighet** väljer **generera/importera**.

3. I den **skapa en hemlighet** rutan i listan över alternativ väljer **manuell**. Om du har skapat tjänsten huvudnamn med hjälp av certifikat, Välj certifikaten från nedrullningsbara listrutan och överför sedan filen.

4. Ange **program-ID** kopieras från tjänstens huvudnamn som namn på din nyckel. Nyckelnamnet kan innehålla endast alfanumeriska tecken och bindestreck (-).

5. Klistra in värdet för nyckeln från tjänstens huvudnamn i den **värdet** fliken.

6. Välj **tjänstens huvudnamn** för den **innehållstyp**.

7. Ange den **Aktiveringsdatum** och **förfallodatum** värden för din nyckel.

8. Välj **skapa** att starta distributionen.

När hemligheten har skapats, lagras information om tjänstens huvudnamn där. Du kan välja det när som helst under **hemligheter**, och visa eller ändra dess egenskaper. Egenskapsavsnittet innehåller hemlig identifierare som är en identifierare URI (Uniform Resource) som externa program använder för att komma åt den här hemligheten.

## <a name="next-steps"></a>Nästa steg

- [Använda tjänstens huvudnamn](azure-stack-create-service-principals.md)
- [Hantera Nyckelvalv i Azure Stack-portalen](azure-stack-key-vault-manage-portal.md)  
- [Hantera Nyckelvalv i Azure Stack med hjälp av PowerShell](azure-stack-key-vault-manage-powershell.md)