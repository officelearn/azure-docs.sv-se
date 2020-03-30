---
title: Skapa en gemensam identitet
titleSuffix: Azure Data Science Virtual Machine
description: Lär dig hur du skapar vanliga användarkonton som kan användas i flera virtuella datavetenskapsdatorer. Du kan använda Azure Active Directory eller en lokal Active Directory för att autentisera användare till den virtuella datorn för datavetenskap.
keywords: djupinlärning, AI, datavetenskapsverktyg, virtuell datavetenskap, geospatial analys, teamdatavetenskapsprocess
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 05/08/2018
ms.openlocfilehash: 44f1f7ae3b290e1dbf01877f3881e1d95a238446
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "70208138"
---
# <a name="set-up-a-common-identity-on-a-data-science-virtual-machine"></a>Konfigurera en gemensam identitet på en virtuell data science-dator

På en virtuell dator (VM), inklusive en virtuell dator (DSVM) (Data Science Virtual Machine), skapar du lokala användarkonton när du etablerar den virtuella datorn. Användare autentiserar sedan till den virtuella datorn med hjälp av dessa autentiseringsuppgifter. Om du har flera virtuella datorer som användarna behöver komma åt kan det bli mycket besvärligt att hantera autentiseringsuppgifter. En utmärkt lösning är att distribuera vanliga användarkonton och hantering via en standardbaserad identitetsleverantör. Med den här metoden kan du använda en enda uppsättning autentiseringsuppgifter för att komma åt flera resurser på Azure, inklusive flera DSVMs.

Active Directory är en populär identitetsprovider och stöds på Azure både som en molntjänst och som en lokal katalog. Du kan använda Azure Active Directory (Azure AD) eller lokala Active Directory för att autentisera användare i en fristående DSVM eller ett kluster av DSVM:er i en Azure-skalningsuppsättning för virtuella datorer. Det gör du genom att ansluta DSVM-instanserna till en Active Directory-domän.

Om du redan har Active Directory kan du använda den som din vanliga identitetsprovider. Om du inte har Active Directory kan du köra en hanterad Active Directory-instans på Azure via [Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/) (Azure AD DS).

Dokumentationen för [Azure AD](https://docs.microsoft.com/azure/active-directory/) innehåller detaljerade [hanteringsinstruktioner,](https://docs.microsoft.com/azure/active-directory/choose-hybrid-identity-solution)inklusive vägledning om hur du ansluter Azure AD till din lokala katalog om du har en.

I den här artikeln beskrivs hur du konfigurerar en fullständigt hanterad Active Directory-domäntjänst på Azure med hjälp av Azure AD DS. Du kan sedan ansluta till dsvm:erna till den hanterade Active Directory-domänen. Med den här metoden kan användare komma åt en pool med DSVM -moduler (och andra Azure-resurser) via ett gemensamt användarkonto och autentiseringsuppgifter.

## <a name="set-up-a-fully-managed-active-directory-domain-on-azure"></a>Konfigurera en fullständigt hanterad Active Directory-domän på Azure

Azure AD DS gör det enkelt att hantera dina identiteter genom att tillhandahålla en fullständigt hanterad tjänst på Azure. På den här Active Directory-domänen hanterar du användare och grupper. Så här konfigurerar du en Azure-värdbaserad Active Directory-domän och användarkonton i katalogen:

1. Lägg till användaren i Active Directory i Azure-portalen: 

   1. Logga in på [Administrationscentret](https://aad.portal.azure.com) för Azure Active Directory med hjälp av ett konto som är en global administratör för katalogen.
    
   1. Välj **Azure Active Directory** och sedan **Användare och grupper**.
    
   1. I **Användare och grupper**väljer du Alla **användare**och väljer sedan Ny **användare**.
   
           The **User** pane opens:
      
      ![Fönstret "Användare"](./media/add-user.png)
    
   1. Ange information för användaren, till exempel **Namn** och **Användarnamn**. Domännamnsdelen av användarnamnet måste vara antingen det ursprungliga standarddomännamnet "[domännamn].onmicrosoft.com" eller ett verifierat, icke-federerat [anpassat domännamn](../../active-directory/add-custom-domain.md) som "contoso.com".
    
   1. Kopiera eller anteckna det genererade användarlösenordet på annat sätt så att du kan ge det till användaren när den här processen är klar.
    
   1. Du kan också öppna och fylla i informationen i **Profil**, **Grupper** eller **Katalogroll** för användaren. 
    
   1. Under **Användare**väljer du **Skapa**.
    
   1. Distribuera det genererade lösenordet till den nya användaren på ett säkert sätt så att de kan logga in.

1. Skapa en Azure AD DS-instans. Följ instruktionerna i [Aktivera Azure Active Directory Domain Services med hjälp av Azure-portalen](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started) (avsnittet "Skapa en instans och konfigurera grundläggande inställningar". Det är viktigt att uppdatera befintliga användarlösenord i Active Directory så att lösenordet i Azure AD DS synkroniseras. Det är också viktigt att lägga till DNS i Azure AD DS, enligt beskrivningen under "Fyll i fälten i fönstret Grunderna i Azure-portalen för att skapa en Azure AD DS-instans" i det avsnittet.

1. Skapa ett separat DSVM-undernät i det virtuella nätverk som skapas i avsnittet "Skapa och konfigurera det virtuella nätverket" i föregående steg.
1. Skapa en eller flera DSVM-instanser i DSVM-undernätet.
1. Följ [instruktionerna](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-join-ubuntu-linux-vm ) för att lägga till DSVM i Active Directory. 
1. Montera en Azure-filresurs som värd för din hem- eller anteckningsbokskatalog så att arbetsytan kan monteras på vilken dator som helst. (Om du behöver snäva behörigheter på filnivå måste du köra Network File System [NFS] på en eller flera virtuella datorer.)

   1. [Skapa en Azure-filresurs](../../storage/files/storage-how-to-create-file-share.md).
    
   2.  Montera den här resursen på Linux DSVM. När du väljer **Anslut** för Azure Files-resursen i ditt lagringskonto i Azure-portalen visas kommandot för att köra i bash-skalet på Linux DSVM. Kommandot ser ut så här:
   
   ```
   sudo mount -t cifs //[STORAGEACCT].file.core.windows.net/workspace [Your mount point] -o vers=3.0,username=[STORAGEACCT],password=[Access Key or SAS],dir_mode=0777,file_mode=0777,sec=ntlmssp
   ```
1. Anta till exempel att du har monterat din Azure-filresurs i /data/workspace. Skapa nu kataloger för var och en av användarna i resursen: /data/workspace/user1, /data/workspace/user2 och så vidare. Skapa `notebooks` en katalog på varje användares arbetsyta. 
1. Skapa symboliska `notebooks` `$HOME/userx/notebooks/remote`länkar för i .   

Du har nu användarna i Active Directory-instansen som finns i Azure. Med Active Directory-autentiseringsuppgifter kan användare logga in på alla DSVM (SSH eller JupyterHub) som är anslutna till Azure AD DS. Eftersom användararbetsytan finns på en Azure Files-resurs har användarna åtkomst till sina anteckningsböcker och annat arbete från alla DSVM när de använder JupyterHub.

För automatisk skalning kan du använda en skalningsuppsättning för virtuella datorer för att skapa en pool med virtuella datorer som alla är anslutna till domänen på det här sättet och med den delade disken monterad. Användare kan logga in på valfri tillgänglig dator i skalningsuppsättningen för den virtuella datorn och få åtkomst till den delade disken där deras anteckningsböcker sparas. 

## <a name="next-steps"></a>Nästa steg

* [Lagra autentiseringsuppgifter för att komma åt molnresurser på ett säkert sätt](dsvm-secure-access-keys.md)



