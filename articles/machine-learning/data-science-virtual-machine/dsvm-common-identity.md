---
title: Konfigurera en gemensam identitet för den virtuella datorn för datavetenskap – Azure | Microsoft Docs
description: Lär dig mer om att skapa vanliga användarkonton som kan användas i flera virtuella datorer för datavetenskap. Du kan använda Azure Active Directory eller en lokal Active Directory för att autentisera användare till den virtuella datorn för datavetenskap.
keywords: djupinlärning, AI, verktyg för datavetenskap, virtuell dator för datavetenskap, geospatial analys, tdsp
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: gokuma
ms.openlocfilehash: 2c981d33c8108386e0287d4c2a800f065c9dc431
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55452863"
---
# <a name="set-up-a-common-identity-on-the-data-science-virtual-machine"></a>Konfigurera en gemensam identitet på den virtuella datorn för datavetenskap

På en Azure-dator (VM), inklusive Data Science Virtual Machine (DSVM), skapa lokala användarkonton när du etablerar den virtuella datorn. Användare autentisera sedan till den virtuella datorn med hjälp av autentiseringsuppgifterna. Om du har flera virtuella datorer som du behöver komma åt snabbt den här metoden besvärlig som du hanterar autentiseringsuppgifter. Vanliga användarkonton och -hantering via en standardbaserad identitetsprovider kan du använda en enda uppsättning autentiseringsuppgifter för att komma åt flera resurser i Azure, inklusive flera Dsvm. 

Active Directory är en populär identitetsprovider och stöds på Azure som en tjänst och lokalt. Du kan använda Azure Active Directory (AD Azure) eller en lokal Active Directory för att autentisera användare på en fristående DSVM eller ett Dsvm-kluster i en Azure VM-skalningsuppsättning. Du kan göra detta genom att koppla DSVM-instanser till en Active Directory-domän. 

Om du redan har Active Directory för att hantera identiteter kan använda du den som vanliga identitetsprovider. Om du inte har Active Directory, kan du köra en hanterad Active Directory-instans på Azure via en tjänst som kallas [Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/) (Azure AD DS). 

I dokumentationen för [Azure AD](https://docs.microsoft.com/azure/active-directory/) ger detaljerad [management instruktioner](https://docs.microsoft.com/azure/active-directory/choose-hybrid-identity-solution#synchronized-identity), inklusive ansluter Azure AD till din lokala katalog om du har en. 

Den här artikeln beskriver stegen för att konfigurera en helt hanterad tjänst på Azure gäller Active Directory-domän med hjälp av Azure AD DS. Du kan sedan ansluta till din Dsvm till hanterade Active Directory-domänen för att ge användare åtkomst till en pool med Dsvm (och andra Azure-resurser) med hjälp av ett vanligt användarkonto och autentiseringsuppgifter. 

## <a name="set-up-a-fully-managed-active-directory-domain-on-azure"></a>Konfigurera en fullständigt hanterad Active Directory-domän i Azure

Azure AD DS gör det enkelt att hantera identiteter genom att tillhandahålla en fullständigt hanterad tjänst på Azure. På den här Active Directory-domän hantera användare och grupper. Stegen för att konfigurera en Azure-baserad Active Directory-domän- och användarkonton i din katalog är:

1. Lägg till användaren till Active Directory i Azure-portalen: 

   a. Logga in på [Azure Active Directory-administratörscentret](https://aad.portal.azure.com) med ett konto som är en global administratör för katalogen.
    
   b. Välj **Azure Active Directory** och sedan **Användare och grupper**.
    
   c. På **Användare och grupper** väljer du **Alla användare** och sedan **Ny användare**.
   
      Den **användaren** öppnas fönstret.
      
      ![Fönstret ”användare”](./media/add-user.png)
    
   d. Ange information för användaren, till exempel **Namn** och **Användarnamn**. Domännamndelen av användarnamnet måste vara den initiala domänen namnet ”[namn].onmicrosoft.com” eller en verifierad icke-federerade [domännamn](../../active-directory/add-custom-domain.md) , till exempel ”contoso.com”.
    
   e. Kopiera eller anteckna det genererade användarlösenordet på annat sätt så att du kan ge det till användaren när den här processen är klar.
    
   f. Du kan också öppna och fylla i informationen i **Profil**, **Grupper** eller **Katalogroll** för användaren. 
    
   g. På **Användare** väljer du **Skapa**.
    
   h. Distribuera det genererade lösenordet på ett säkert sätt till den nya användaren så att användaren kan logga in.

1. Skapa en Azure AD DS-instans. Följ instruktionerna i artikeln [aktivera Azure Active Directory Domain Services med Azure portal](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started) (uppgifter 1 till 5). Det är viktigt att uppdatera de befintliga lösenord i Active Directory så att lösenordet i Azure AD DS har synkroniserats. Det är också viktigt att lägga till DNS i Azure AD DS, enligt beskrivningen i uppgift 4 av artikeln. 

1. Skapa ett separat DSVM-undernät i det virtuella nätverket som skapades i uppgift 2 i det föregående steget.
1. Skapa en eller flera Data Science VM-instanser i DSVM-undernät. 
1. Följ den [instruktioner](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-join-ubuntu-linux-vm ) att lägga till DSVM i Active Directory. 
1. Montera en Azure Files-resurs som värd för katalogen hemmet eller anteckningsboken för att aktivera montera din arbetsyta på en dator. (Om du behöver nära behörigheter måste NFS som körs på en eller flera virtuella datorer.)

   a. [Skapa en Azure Files-resurs](../../storage/files/storage-how-to-create-file-share.md).
    
   b. Montera den på Linux DSVM. När du väljer den **Connect** knappen för Azure Files dela i ditt storage-konto i Azure-portalen, kommandot för att köra i Bash shell på Linux DSVM visas. Kommandot ser ut så här:
   
   ```
   sudo mount -t cifs //[STORAGEACCT].file.core.windows.net/workspace [Your mount point] -o vers=3.0,username=[STORAGEACCT],password=[Access Key or SAS],dir_mode=0777,file_mode=0777,sec=ntlmssp
   ```
1. Anta att du har monterat Azure Files-resurs i /data/workspace, till exempel. Nu skapa kataloger för var och en av användarna i resursen: /data/workspace/user1, /data/workspace/user2 och så vidare. Skapa en `notebooks` katalogen på varje användares arbetsyta. 
1. Skapa symboliska länkar för `notebooks` i `$HOME/userx/notebooks/remote`.   

Nu kan har du användare i din Active Directory-instans i Azure. Med hjälp av Active Directory-autentiseringsuppgifter, kan användare logga in på alla DSVM (SSH eller JupyterHub) som är ansluten till Azure AD DS. Eftersom användararbetsytan som är på en Azure Files-resurs, har användare åtkomst till sina bärbara datorer och annat arbete från alla DSVM när de använder JupyterHub. 

Du kan använda en VM-skalningsuppsättning att skapa en pool med virtuella datorer som är anslutna till domänen på detta sätt och med delad disk monterad för automatisk skalning. Användare kan logga in på alla tillgängliga datorer i virtual machine scale Sets och har åtkomst till den delade disken var sina bärbara datorer ska sparas. 

## <a name="next-steps"></a>Nästa steg

* [På ett säkert sätt lagra autentiseringsuppgifter för att komma åt resurser i molnet](dsvm-secure-access-keys.md)



