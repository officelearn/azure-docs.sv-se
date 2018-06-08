---
title: Konfigurera gemensam identitet för datavetenskap Virtual Machine - Azure | Microsoft Docs
description: Konfigurera gemensam identitet i enterprise-teamet DSVM miljöer.
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
ms.openlocfilehash: 70c6b8cd147cefaa3128bc1e6a414a6fa61dba6d
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2018
ms.locfileid: "34830902"
---
# <a name="setup-common-identity-on-the-data-science-vm"></a>Konfigurera en gemensam identitet på den virtuella datorn vetenskap Data

Som standard på Azure VM, inklusive den lokala användaren Data vetenskap VM (DSVM) konton skapas vid etablering av den virtuella datorn och användarna autentiseras på den virtuella datorn med de här autentiseringsuppgifterna. Om du har flera virtuella datorer som behöver åtkomst till snabbt den här metoden krånglig att hantera autentiseringsuppgifter. Vanliga användarkonton och hantering med hjälp av en standardbaserad identitetsleverantör kan du använda en enda uppsättning autentiseringsuppgifter för åtkomst till flera resurser på Azure, inklusive flera DSVMs. 

Active Directory (AD) är en populär identitetsleverantör och stöds både på Azure som en tjänst som lokalt. Du kan utnyttja AD eller Azure AD för att autentisera användare på en fristående Data vetenskap VM (DSVM) eller i ett kluster med DSVM i en skaluppsättning för virtuell dator i Azure. Detta görs genom att koppla DSVM instanser till en AD-domän. Om du redan har en Active Directory för att hantera identiteter kan du använda den som vanliga identitetsprovider. Om du inte har en Annons kan du köra en hanterad AD på Azure via en tjänst som kallas [Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/)(Azure AD DS). 

I dokumentationen för [Azure Active directory (AD Azure)](https://docs.microsoft.com/azure/active-directory/) ger detaljerad [instruktioner](https://docs.microsoft.com/azure/active-directory/choose-hybrid-identity-solution#synchronized-identity) hantera active directory, inklusive ansluter Azure AD till din lokala katalog om du har en. 

Resten av den här artikeln beskriver stegen för att ställa in en helt hanterad AD-domän-tjänst på Azure med hjälp av Azure AD DS och ansluta din DSVMs till hanterade AD-domänen för att ge användare åtkomst till en pool med DSVMs (och andra Azure-resurser) med hjälp av ett vanligt användarkonto och autentiseringsuppgifter. 

##  <a name="set-up-a-fully-managed-active-directory-domain-on-azure"></a>Konfigurera en helt hanterad Active Directory-domän på Azure

Azure AD DS gör det enkelt att hantera identiteter genom att tillhandahålla en helt hanterad tjänst i Azure. På den här Active directory-domän hanteras användare och grupper.  Stegen för att konfigurera en Azure AD-domän som värd och användarkonton i din katalog är:

1. Lägga till användare i Active directory på portalen 

    a. Logga in på den [Azure Active Directory Administrationscenter](https://aad.portal.azure.com) med ett konto som är en global administratör för katalogen.
    
    b. Välj **Azure Active Directory** och sedan **användare och grupper**.
    
    c. På **användare och grupper**väljer **alla användare**, och välj sedan **ny användare**.
   
   ![Att välja kommandot Lägg till](./media/add-user.png)
    
    d. Ange information för användaren, till exempel **namn** och **användarnamn**. Domännamndelen för användarnamn måste antingen vara den första standard domänen namnet ”[namn].onmicrosoft.com” eller en verifierad ofedererad [domännamn](../../active-directory/add-custom-domain.md) , till exempel ”contoso.com”.
    
    e. Kopiera eller annars Observera genererade användarens lösenord så att du kan ange den till användaren när processen har slutförts.
    
    f. Du kan också öppna och fylla i informationen i **profil**, **grupper**, eller **Directory rollen** för användaren. 
    
    g. På **användare**väljer **skapa**.
    
    h. På ett säkert sätt distribuera genererade lösenordet till den nya användaren så att användaren kan logga in.

2.  Skapa Azure AD Domain Services

    Om du vill skapa ett Azure lägger till, följer du anvisningarna i artikeln ”[aktivera Azure Active Directory Domain Services med Azure-portalen](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started)” (aktivitet 1 till uppgift 5). Det är viktigt att de befintliga lösenord i Active directory uppdateras så att lösenord i Azure AD DS är synkroniserade. Det är också viktigt att lägga till DNS i Azure AD DS som anges i aktiviteten #4 i artikeln ovan. 

3.  Skapa ett separat DSVM undernät i det virtuella nätverket som skapats i aktiviteten #2 i föregående steg
4.  Skapa en eller flera datavetenskap VM-instanser i DSVM undernät 
5.  Följ [instruktioner](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-join-ubuntu-linux-vm ) att lägga till DSVM i AD. 
6.  Sedan montera en delade filer i Azure som värd för katalogen home eller bärbar dator om du vill aktivera montera din arbetsyta på en dator. (Om du behöver nära nivå filbehörigheter du behöver en NFS som körs på en eller flera virtuella datorer)

    a. [Skapa en Azure-filresurs](../../storage/files/storage-how-to-create-file-share.md)
    
    b. Montera den på Linux DSVM. Kommandot för att köra bash Shell på Linux DSVM visas när du klickar på knappen ”Anslut” för Azure-filer i ditt lagringskonto på Azure-portalen. Kommandot ser ut så här:
```
sudo mount -t cifs //[STORAGEACCT].file.core.windows.net/workspace [Your mount point] -o vers=3.0,username=[STORAGEACCT],password=[Access Key or SAS],dir_mode=0777,file_mode=0777,sec=ntlmssp
```
7.  Anta att du har monterat dina Azure-filer i /data/workspace. Nu skapa kataloger för varje användare i resursen. /data/Workspace/User1, /data/workspace/user2 och så vidare. Skapa en ```notebooks``` katalog i arbetsytan för varje användare. 
8. Skapa symboliska länkar för den ```notebooks``` i ```$HOME/userx/notebooks/remote```.   

Nu kan har du användare i active directory finns i Azure och logga in till alla DSVM (SSH, Jupyterhub) som är ansluten till Azure AD DS med hjälp av AD-autentiseringsuppgifter. Eftersom arbetsytan användaren finns på delade filer i Azure, måste ha användaren åtkomst till sina bärbara datorer och annat arbete från alla DSVM när du använder Jupyterhub. 

Du kan använda virtuella datorns skaluppsättning vill skapa en pool för virtuella datorer som är anslutna till domänen på detta sätt och med den delade disken monterade för automatisk skalning. Användare kan logga in på en dator som är tillgängliga i virtuella datorns skaluppsättning och har åtkomst till delade disken var sina bärbara datorer ska sparas. 

## <a name="next-steps"></a>Nästa steg

* [Lagra autentiseringsuppgifter för att få åtkomst till molnresurser](dsvm-secure-access-keys.md)



