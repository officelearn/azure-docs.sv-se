---
title: Konfigurera en gemensam identitet
titleSuffix: Azure Data Science Virtual Machine
description: Lär dig mer om att skapa vanliga användarkonton som kan användas i flera virtuella datorer för datavetenskap. Du kan använda Azure Active Directory eller en lokal Active Directory för att autentisera användare till den virtuella datorn för datavetenskap.
keywords: djupinlärning, AI, verktyg för datavetenskap, virtuell dator för datavetenskap, geospatial analys, tdsp
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 05/08/2018
ms.openlocfilehash: 44f1f7ae3b290e1dbf01877f3881e1d95a238446
ms.sourcegitcommit: 5f67772dac6a402bbaa8eb261f653a34b8672c3a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/01/2019
ms.locfileid: "70208138"
---
# <a name="set-up-a-common-identity-on-a-data-science-virtual-machine"></a>Konfigurera en gemensam identitet på en Data Science Virtual Machine

På en Microsoft Azure virtuell dator (VM), inklusive en Data Science Virtual Machine (DSVM), skapar du lokala användar konton när du konfigurerar den virtuella datorn. Användare autentisera sedan till den virtuella datorn med hjälp av autentiseringsuppgifterna. Om du har flera virtuella datorer som dina användare behöver åtkomst till kan hanteringen av autentiseringsuppgifter bli mycket besvärlig. En utmärkt lösning är att distribuera vanliga användar konton och hantering via en standardbaserad identitets leverantör. Med den här metoden kan du använda en enda uppsättning autentiseringsuppgifter för att få åtkomst till flera resurser i Azure, inklusive flera Dsvm.

Active Directory är en populär identitets leverantör som stöds i Azure både som en moln tjänst och som en lokal katalog. Du kan använda Azure Active Directory (AD Azure) eller en lokal Active Directory för att autentisera användare på en fristående DSVM eller ett Dsvm-kluster i en Azure VM-skalningsuppsättning. Du kan göra detta genom att koppla DSVM-instanser till en Active Directory-domän.

Om du redan har Active Directory kan du använda den som din gemensamma identitets leverantör. Om du inte har Active Directory kan du köra en hanterad Active Directory-instans på Azure via [Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/) (Azure AD DS).

Dokumentationen för [Azure AD](https://docs.microsoft.com/azure/active-directory/) innehåller detaljerade [hanterings anvisningar](https://docs.microsoft.com/azure/active-directory/choose-hybrid-identity-solution), inklusive vägledning om hur du ansluter Azure AD till din lokala katalog om du har en.

Den här artikeln beskriver hur du konfigurerar en fullständigt hanterad Active Directory domän tjänst på Azure med hjälp av Azure AD DS. Du kan sedan ansluta din Dsvm till den hanterade Active Directorys domänen. Den här metoden gör det möjligt för användare att komma åt en pool med Dsvm (och andra Azure-resurser) via ett gemensamt användar konto och autentiseringsuppgifter.

## <a name="set-up-a-fully-managed-active-directory-domain-on-azure"></a>Konfigurera en fullständigt hanterad Active Directory-domän i Azure

Azure AD DS gör det enkelt att hantera identiteter genom att tillhandahålla en fullständigt hanterad tjänst på Azure. På den här Active Directory-domän hantera användare och grupper. Följ dessa steg om du vill konfigurera en Azure-värdbaserad Active Directory domän och användar konton i din katalog:

1. Lägg till användaren till Active Directory i Azure-portalen: 

   1. Logga in på [Azure Active Directory administrations Center](https://aad.portal.azure.com) genom att använda ett konto som är en global administratör för katalogen.
    
   1. Välj **Azure Active Directory** och sedan **Användare och grupper**.
    
   1. I **användare och grupper**väljer du **alla användare**och väljer sedan **ny användare**.
   
           The **User** pane opens:
      
      ![Fönstret ”användare”](./media/add-user.png)
    
   1. Ange information för användaren, till exempel **Namn** och **Användarnamn**. Domännamndelen av användarnamnet måste vara den initiala domänen namnet ”[namn].onmicrosoft.com” eller en verifierad icke-federerade [domännamn](../../active-directory/add-custom-domain.md) , till exempel ”contoso.com”.
    
   1. Kopiera eller anteckna det genererade användarlösenordet på annat sätt så att du kan ge det till användaren när den här processen är klar.
    
   1. Du kan också öppna och fylla i informationen i **Profil**, **Grupper** eller **Katalogroll** för användaren. 
    
   1. Under **användare**väljer du **skapa**.
    
   1. Distribuera det genererade lösen ordet på ett säkert sätt till den nya användaren så att de kan logga in.

1. Skapa en Azure AD DS-instans. Följ anvisningarna i [aktivera Azure Active Directory Domain Services med hjälp av Azure Portal](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started) (avsnittet "skapa en instans och konfigurera grundläggande inställningar"). Det är viktigt att uppdatera de befintliga lösenord i Active Directory så att lösenordet i Azure AD DS har synkroniserats. Det är också viktigt att lägga till DNS i Azure AD DS, enligt beskrivningen under "Fyll i fälten i fönstret Basics i Azure Portal för att skapa en Azure AD DS-instans" i avsnittet.

1. Skapa ett separat DSVM-undernät i det virtuella nätverk som skapats i avsnittet "skapa och konfigurera det virtuella nätverket" i föregående steg.
1. Skapa en eller flera DSVM-instanser i DSVM-undernätet.
1. Följ [anvisningarna](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-join-ubuntu-linux-vm ) för att lägga till DSVM i Active Directory. 
1. Montera en Azure Files-resurs som värd för din hem-eller Notebook-katalog så att din arbets yta kan monteras på vilken dator som helst. (Om du behöver tätt fil nivå behörigheter behöver du använda Network File System [NFS] på en eller flera virtuella datorer.)

   1. [Skapa en Azure Files-resurs](../../storage/files/storage-how-to-create-file-share.md).
    
   2.  Montera den här resursen på Linux-DSVM. När du väljer **Anslut** för Azure Files resursen på ditt lagrings konto i Azure Portal visas kommandot som ska köras i bash-gränssnittet på Linux-DSVM. Kommandot ser ut så här:
   
   ```
   sudo mount -t cifs //[STORAGEACCT].file.core.windows.net/workspace [Your mount point] -o vers=3.0,username=[STORAGEACCT],password=[Access Key or SAS],dir_mode=0777,file_mode=0777,sec=ntlmssp
   ```
1. Anta till exempel att du har monterat din Azure Files-resurs i/data/Workspace. Skapa nu kataloger för var och en av dina användare i resursen:/data/Workspace/user1,/data/Workspace/user2 och så vidare. Skapa en `notebooks` katalogen på varje användares arbetsyta. 
1. Skapa symboliska länkar för `notebooks` i `$HOME/userx/notebooks/remote`.   

Nu har du användare i din Active Directory-instans som finns i Azure. Genom att använda Active Directory autentiseringsuppgifter kan användare logga in på alla DSVM (SSH eller JupyterHub) som är anslutna till Azure AD DS. Eftersom användararbetsytan som är på en Azure Files-resurs, har användare åtkomst till sina bärbara datorer och annat arbete från alla DSVM när de använder JupyterHub.

Du kan använda en VM-skalningsuppsättning att skapa en pool med virtuella datorer som är anslutna till domänen på detta sätt och med delad disk monterad för automatisk skalning. Användare kan logga in på alla tillgängliga datorer i skalnings uppsättningen för den virtuella datorn och ha åtkomst till den delade disk där deras antecknings böcker har sparats. 

## <a name="next-steps"></a>Nästa steg

* [På ett säkert sätt lagra autentiseringsuppgifter för att komma åt resurser i molnet](dsvm-secure-access-keys.md)



