---
title: Azure AD Connect moln etablering ny agent konfiguration
description: Den här artikeln beskriver hur du installerar Cloud-etablering.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/26/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: c8b18629a776dd98950f49b1f607cbc876abcd9c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91628915"
---
# <a name="create-a-new-configuration-for-azure-ad-connect-cloud-based-provisioning"></a>Skapa en ny konfiguration för Azure AD Connect molnbaserad etablering

När du har installerat agenten måste du logga in på Azure Portal och konfigurera Azure Active Directory (Azure AD) Anslut till moln etablering. Följ dessa steg om du vill aktivera agenten.

## <a name="configure-provisioning"></a>Konfigurera etablering
Följ dessa steg om du vill konfigurera etableringen.

 1. I Azure Portal väljer du **Azure Active Directory**
 2. Välj **Azure AD Connect**.
 3. Välj **Hantera etablering**.

 ![Hantera etablering](media/how-to-configure/manage1.png)
 
 4. Välj **ny konfiguration**.
 5. På sidan konfiguration väljer du din domän och anger om du vill aktivera hash-synkronisering av lösen ord.  Klicka på **skapa**.  
 
 ![Skapa ny konfiguration](media/how-to-configure/configure1.png)


 6.  Konfigurations skärmen för att redigera etablering öppnas.

   ![Redigera konfiguration](media/how-to-configure/configure2.png)

 7. Ange ett **e-postmeddelande för avisering**. Det här e-postmeddelandet kommer att meddelas när etableringen inte är felfritt.
 8. Flytta väljaren för att aktivera och välj Spara.

## <a name="scope-provisioning-to-specific-users-and-groups"></a>Omfattnings etablering för vissa användare och grupper
Du kan begränsa agenten till att synkronisera vissa användare och grupper genom att använda lokala Active Directory grupper eller organisationsenheter. Du kan inte konfigurera grupper och organisationsenheter i en konfiguration. 

 1.  I Azure Portal väljer du **Azure Active Directory**.
 2. Välj **Azure AD Connect**.
 3. Välj **Hantera etablering (för hands version)**.
 4. Under **konfiguration**väljer du din konfiguration.

 ![Konfigurations avsnitt](media/how-to-configure/scope1.png)
 
 5. Under **Konfigurera**väljer du **Redigera omfångs filter** för att ändra omfånget för konfigurations regeln.
 ![Redigera omfattning](media/how-to-configure/scope3.png)
 7. Till höger kan du ändra omfattningen.  Klicka på **klart**  och **Spara** när du är färdig.
 8. När du har ändrat omfattning bör du [starta om etableringen](#restart-provisioning) för att initiera en omedelbar synkronisering av ändringarna.

## <a name="attribute-mapping"></a>Attributmappning
Med Azure AD Connect Cloud etableringen kan du enkelt mappa attribut mellan dina lokala användar-och grupp objekt och objekten i Azure AD.  Du kan anpassa standardattributen – mappningar efter dina affärs behov. Så du kan ändra eller ta bort befintliga attribut-mappningar eller skapa nya attribut-mappningar.  Mer information finns i [attribut mappning](how-to-attribute-mapping.md).

## <a name="on-demand-provisioning"></a>Etablering på begäran
Med Azure AD Connect Cloud-etablering kan du testa konfigurations ändringar genom att tillämpa dessa ändringar på en enskild användare eller grupp.  Du kan använda detta för att verifiera och kontrol lera att ändringarna som har gjorts i konfigurationen har tillämpats korrekt och synkroniseras korrekt med Azure AD.  Mer information finns i [etablering på begäran](how-to-on-demand-provision.md).

## <a name="restart-provisioning"></a>Starta om etablering 
Om du inte vill vänta på nästa schemalagda körning utlöser du etablerings körningen med hjälp av knappen **starta om etablering** . 
 1.  I Azure Portal väljer du **Azure Active Directory**.
 2. Välj **Azure AD Connect**.
 3.  Välj **Hantera etablering (för hands version)**.
 4. Under **konfiguration**väljer du din konfiguration.

   ![Konfigurations val för att starta om etableringen](media/how-to-configure/scope1.png)

 5. Välj **starta om etablering**längst upp.

## <a name="remove-a-configuration"></a>Ta bort en konfiguration
Följ dessa steg om du vill ta bort en konfiguration.

 1.  I Azure Portal väljer du **Azure Active Directory**.
 2. Välj **Azure AD Connect**.
 3. Välj **Hantera etablering (för hands version)**.
 4. Under **konfiguration**väljer du din konfiguration.
   
   ![Konfigurations val för att ta bort konfiguration](media/how-to-configure/scope1.png)

 5. Klicka på **ta bort**överst på konfigurations skärmen.

>[!IMPORTANT]
>Det finns ingen bekräftelse innan du tar bort en konfiguration. Kontrol lera att detta är den åtgärd du vill vidta innan du väljer **ta bort**.


## <a name="next-steps"></a>Nästa steg 

- [Vad är etablering?](what-is-provisioning.md)
- [Vad är Azure AD Connect-molnetablering?](what-is-cloud-provisioning.md)
