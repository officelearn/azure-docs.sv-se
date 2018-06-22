---
title: Konfigurera en gemensam identitet för den datavetenskap virtuella - Azure | Microsoft Docs
description: Ställ in en gemensam identitet i en enterprise-teamet DSVM miljöer.
keywords: Djup learning AI datavetenskap verktyg, datavetenskap virtuell dator, geospatiala analytics, team av vetenskapliga data
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: gokuma
ms.openlocfilehash: d6235f3a425481a13e627d683bb4c3943b473b40
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/21/2018
ms.locfileid: "36309829"
---
# <a name="set-up-a-common-identity-on-the-data-science-virtual-machine"></a>Konfigurera en gemensam identitet på den datavetenskap virtuella datorn

På en Azure-dator (VM), inklusive den Data vetenskap virtuell dator (DSVM), skapa lokala användarkonton vid etablering av den virtuella datorn. Användare autentiseras sedan till den virtuella datorn med hjälp av autentiseringsuppgifterna. Om du har flera virtuella datorer som behöver åtkomst till snabbt den här metoden besvärlig när du hanterar autentiseringsuppgifter. Vanliga användarkonton och -hantering via en standardbaserad identitetsleverantör kan du använda en enda uppsättning autentiseringsuppgifter för åtkomst till flera resurser på Azure, inklusive flera DSVMs. 

Active Directory är en populär identitetsleverantör och stöds på Azure som en tjänst och lokalt. Du kan använda Azure Active Directory (AD Azure) eller lokal Active Directory för att autentisera användare på en fristående DSVM eller ett kluster med DSVMs i en skaluppsättning för virtuell dator i Azure. Det gör du genom att koppla DSVM instanser till en Active Directory-domän. 

Om du redan har Active Directory för att hantera identiteter kan du använda den som vanliga identitetsprovider. Om du inte har Active Directory, kan du köra en hanterad Active Directory-instans på Azure via tjänsten [Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/) (Azure AD DS). 

I dokumentationen för [Azure AD](https://docs.microsoft.com/azure/active-directory/) ger detaljerad [management instruktioner](https://docs.microsoft.com/azure/active-directory/choose-hybrid-identity-solution#synchronized-identity), inklusive ansluter Azure AD till din lokala katalog om du har en. 

Den här artikeln beskriver stegen för att ställa in en helt hanterad Active Directory domain service på Azure med hjälp av Azure AD DS. Du kan sedan ansluta din DSVMs till hanterade Active Directory-domänen för att ge användare åtkomst till en pool med DSVMs (och andra Azure-resurser) med hjälp av ett vanligt användarkonto och autentiseringsuppgifter. 

## <a name="set-up-a-fully-managed-active-directory-domain-on-azure"></a>Konfigurera en helt hanterad Active Directory-domän på Azure

Azure AD DS gör det enkelt att hantera identiteter genom att tillhandahålla en helt hanterad tjänst i Azure. På den här Active Directory-domän hantera du användare och grupper. Stegen för att konfigurera en Azure-baserad Active Directory-domän- och användarkonton i din katalog är:

1. Lägg till användaren till Active Directory i Azure-portalen: 

   a. Logga in på den [Azure Active Directory Administrationscenter](https://aad.portal.azure.com) med ett konto som är en global administratör för katalogen.
    
   b. Välj **Azure Active Directory** och sedan **användare och grupper**.
    
   c. På **användare och grupper**väljer **alla användare**, och välj sedan **ny användare**.
   
      Den **användaren** fönstret öppnas.
      
      ![Fönstret ”användare”](./media/add-user.png)
    
   d. Ange information för användaren, till exempel **namn** och **användarnamn**. Domännamndelen för användarnamnet måste vara den första standard domänen namnet ”[namn].onmicrosoft.com” eller en verifierad ofedererad [domännamn](../../active-directory/add-custom-domain.md) , till exempel ”contoso.com”.
    
   e. Kopiera eller annars Observera genererade användarens lösenord så att du kan ange den till användaren när processen har slutförts.
    
   f. Du kan också öppna och fylla i informationen i **profil**, **grupper**, eller **Directory rollen** för användaren. 
    
   g. På **användare**väljer **skapa**.
    
   h. På ett säkert sätt distribuera genererade lösenordet till den nya användaren så att användaren kan logga in.

2. Skapa en Azure AD DS-instans. Följ anvisningarna i artikeln [aktivera Azure Active Directory Domain Services med Azure-portalen](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started) (uppgifter 1-5). Det är viktigt att uppdatera de befintliga lösenord i Active Directory så att lösenord i Azure AD DS är synkroniserad. Det är också viktigt att lägga till DNS i Azure AD DS, enligt beskrivningen i uppgift 4 av artikeln. 

3. Skapa ett separat DSVM undernät i det virtuella nätverket som skapats i uppgift 2 i föregående steg.
4. Skapa en eller flera datavetenskap VM-instanser i DSVM undernät. 
5. Följ den [instruktioner](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-join-ubuntu-linux-vm ) att lägga till DSVM i Active Directory. 
6. Montera en resurs för Azure-filer som värd för katalogen home eller bärbar dator om du vill aktivera montera din arbetsyta på en dator. (Om du behöver nära behörigheter måste NFS som körs på en eller flera virtuella datorer.)

   a. [Skapa en resurs i Azure-filer](../../storage/files/storage-how-to-create-file-share.md).
    
   b. Montera den på Linux DSVM. När du väljer den **Anslut** knappen för Azure-filer som delar på ditt lagringskonto i Azure-portalen, kommandot för att köras i Bash shell på Linux DSVM visas. Kommandot ser ut så här:
   
   ```
   sudo mount -t cifs //[STORAGEACCT].file.core.windows.net/workspace [Your mount point] -o vers=3.0,username=[STORAGEACCT],password=[Access Key or SAS],dir_mode=0777,file_mode=0777,sec=ntlmssp
   ```
7. Anta att du monterade resursen Azure-filer i /data/workspace, till exempel. Nu skapa kataloger för varje användare i resursen: /data/workspace/user1, /data/workspace/user2 och så vidare. Skapa en `notebooks` katalog i arbetsytan för varje användare. 
8. Skapa symboliska länkar för `notebooks` i `$HOME/userx/notebooks/remote`.   

Du har nu användarna i din Active Directory-instans som finns på Azure. Med hjälp av Active Directory-autentiseringsuppgifter, kan användare logga in på alla DSVM (SSH eller JupyterHub) som är ansluten till Azure AD DS. Eftersom arbetsytan användare på en resurs i Azure-filer har användare åtkomst till sina bärbara datorer och annat arbete från alla DSVM när de använder JupyterHub. 

Du kan använda en virtuell dator skala vill skapa en pool för virtuella datorer som är anslutna till domänen på detta sätt och med den delade disken monterade för autoskalning. Användare kan logga in på en dator som är tillgängliga i virtuella datorns skaluppsättning och har åtkomst till den delade disken var sina bärbara datorer ska sparas. 

## <a name="next-steps"></a>Nästa steg

* [Lagra autentiseringsuppgifter för att få åtkomst till molnresurser](dsvm-secure-access-keys.md)



