---
title: Felsöka distribution och identifiering av Azure Migrate-enheter
description: Få hjälp med att distribuera Azure Migrate-enheten och identifiera datorer.
author: musa-57
ms.manager: abhemraj
ms.author: hamusa
ms.topic: troubleshooting
ms.date: 01/02/2020
ms.openlocfilehash: 6cb83a87f2e96eb62696e5d92095ef2b8d7c7def
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81677325"
---
# <a name="troubleshoot-the-azure-migrate-appliance-and-discovery"></a>Felsöka Azure Migrate-installationen och identifieringen

Den här artikeln hjälper dig att felsöka problem när du distribuerar [Azure Migrate](migrate-services-overview.md) -installationen och hur du använder-enheten för att identifiera lokala datorer.


## <a name="whats-supported"></a>Vad stöds?

[Granska](migrate-appliance.md) support kraven för installationen.


## <a name="invalid-ovf-manifest-entry"></a>"Ogiltig OVF-manifest post"

Om du får felet "den tillhandahållna manifest filen är ogiltig: ogiltig OVF-manifest post", gör du följande:

1. Kontrol lera att filen med ägg Azure Migrates apparaten laddas ned korrekt genom att kontrol lera dess hash-värde. [Läs mer](https://docs.microsoft.com/azure/migrate/tutorial-assessment-vmware). Om hash-värdet inte matchar laddar du ned den och försöker distribuera igen.
2. Om distributionen fortfarande Miss lyckas och du använder VMware vSphere-klienten för att distribuera OVF-filen kan du prova att distribuera den via vSphere-webbklienten. Om distributionen fortfarande Miss lyckas kan du prova att använda en annan webbläsare.
3. Om du använder vSphere-webbklienten och försöker distribuera den på vCenter Server 6,5 eller 6,7 försöker du att distribuera de ägg som finns direkt på ESXi-värden:
   - Anslut till ESXi-värden direkt (i stället för vCenter Server) med webb klienten (https://<*host IP Address*>/UI).
   - I **hem** > **inventering**väljer du mall för **fil** > **distribution OVF**. Bläddra till de ägg och slutför distributionen.
4. Kontakta Azure Migrate support om distributionen fortfarande Miss lyckas.

## <a name="cant-connect-to-the-internet"></a>Det går inte att ansluta till Internet

Detta kan inträffa om installations datorn finns bakom en proxyserver.

- Se till att du anger autentiseringsuppgifter för auktorisering om proxyn behöver dem.
- Om du använder en URL-baserad brand Väggs-proxy för att kontrol lera utgående anslutningar lägger du till [dessa URL: er](migrate-appliance.md#url-access) i en lista över tillåtna.
- Om du använder en avlyssnings-proxy för att ansluta till Internet importerar du det till den virtuella datorns dator med [de här stegen](https://docs.microsoft.com/azure/migrate/concepts-collector).

## <a name="cant-sign-into-azure-from-the-appliance-web-app"></a>Det går inte att logga in på Azure från produktens webbapp

Felet "Tyvärr! vi har problem med att logga in dig" visas om du använder ett felaktigt Azure-konto för att logga in på Azure. Felet beror på ett par olika orsaker:

- Om du loggar in på enhetens webb program för det offentliga molnet kan du använda användar kontots autentiseringsuppgifter för den offentliga moln portalen.
- Om du loggar in på enhetens webb program för det offentliga molnet med autentiseringsuppgifter för användar kontot för den privata moln portalen.

Se till att du använder rätt autentiseringsuppgifter.

##  <a name="datetime-synchronization-error"></a>Fel vid synkronisering av datum/tid

Ett fel vid synkronisering av datum och tid (802) anger att Server klockan kanske inte är synkroniserad med den aktuella tiden med mer än fem minuter. Ändra klockan på den insamlade datorn så att den matchar den aktuella tiden:

1. Öppna en administratörs kommando tolk på den virtuella datorn.
2. Kör **w32tm/TZ**för att kontrol lera tids zonen.
3. Kör **w32tm/resync**för att synkronisera tiden.


## <a name="unabletoconnecttoserver"></a>"UnableToConnectToServer"

Om du får det här anslutnings felet kanske du inte kan ansluta till vCenter Server *servername*. com: 9443. Fel informationen indikerar att det inte finns någon slut punkt `https://\*servername*.com:9443/sdk` som kan ta emot meddelandet.

- Kontrol lera om du kör den senaste versionen av installationen. Om du inte gör det uppgraderar du installationen till den [senaste versionen](https://docs.microsoft.com/azure/migrate/concepts-collector).
- Om problemet fortfarande uppstår i den senaste versionen kanske installationen inte kan lösa det angivna vCenter Server namnet, eller så kan den angivna porten vara fel. Om porten inte anges försöker insamlaren som standard att ansluta till port nummer 443.

    1. Pinga *servername*. com från enheten.
    2. Om steg 1 Miss lyckas försöker du ansluta till vCenter-servern med hjälp av IP-adressen.
    3. Identifiera rätt port nummer för att ansluta till vCenter Server.
    4. Kontrol lera att vCenter Server är igång.


## <a name="error-6005260039-appliance-might-not-be-registered"></a>Fel 60052/60039: installationen kanske inte kan registreras

- Fel 60052, "installationen kanske inte har registrerats till Azure Migrate projektet" inträffar om det Azure-konto som används för att registrera installationen har otillräcklig behörighet.
    - Se till att Azure-användarkontot som används för att registrera installationen har minst deltagar behörighet för prenumerationen.
    - [Lär dig mer](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance---vmware) om de Azure-roller och behörigheter som krävs.
- Fel 60039, "installationen kanske inte kan registreras Azure Migrate projektet" kan inträffa om registreringen Miss lyckas eftersom det Azure Migrate-projekt som används för att registrera installationen inte kan hittas.
    - I Azure Portal och kontrol lera om projektet finns i resurs gruppen.
    - Om projektet inte finns skapar du ett nytt Azure Migrate-projekt i resurs gruppen och registrerar enheten igen. [Lär dig hur du](https://docs.microsoft.com/azure/migrate/how-to-add-tool-first-time#create-a-project-and-add-a-tool) skapar ett nytt projekt.

## <a name="error-6003060031-key-vault-management-operation-failed"></a>Fel 60030/60031: Key Vault hanterings åtgärd misslyckades

Om du får felet 60030 eller 60031 kan du göra följande för att utföra en Azure Key Vault hanterings åtgärd:
- Kontrol lera att Azure-användarkontot som används för att registrera installationen har minst deltagar behörighet för prenumerationen.
- Kontrol lera att kontot har åtkomst till nyckel valvet som anges i fel meddelandet och försök sedan igen.
- Kontakta Microsofts supportavdelning om problemet kvarstår.
- [Lär dig mer](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance---vmware) om de Azure-roller och behörigheter som krävs.

## <a name="error-60028-discovery-couldnt-be-initiated"></a>Fel 60028: det gick inte att initiera identifiering

Fel 60028: "Det gick inte att initiera identifieringen på grund av ett fel. Åtgärden kunde inte utföras för den angivna listan över värdar eller kluster "anger att identifiering inte kunde startas på värdarna som anges i felet på grund av ett problem med att komma åt eller hämta information om virtuella datorer. Resten av värdarna har lagts till.

- Lägg till värdarna i listan i fel igen med alternativet **Lägg till värd** .
- Om det finns ett verifierings fel kan du läsa igenom reparations vägledningen för att åtgärda felen och sedan försöka **Spara och starta identifierings** alternativet igen.

## <a name="error-60025-azure-ad-operation-failed"></a>Fel 60025: Azure AD-åtgärd misslyckades 
Fel 60025: "en Azure AD-åtgärd misslyckades. Felet inträffade när Azure AD-programmet skapades eller uppdaterades när Azure-användarkontot som används för att initiera identifieringen skiljer sig från det konto som används för att registrera installationen. Gör något av följande:

- Se till att det användar konto som initierar identifieringen är samma som det som används för att registrera enheten.
- Ange åtkomst behörighet för Azure Active Directory program till det användar konto som identifierings åtgärden misslyckades för.
- Ta bort resurs gruppen som skapades tidigare för det Azure Migrate projektet. Skapa en annan resurs grupp för att starta igen.
- [Läs mer](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance---vmware) om Azure Active Directory program behörigheter.


## <a name="error-50004-cant-connect-to-host-or-cluster"></a>Fel 50004: det går inte att ansluta till värd eller kluster

Fel 50004: "det går inte att ansluta till en värd eller ett kluster eftersom Server namnet inte kan matchas. WinRM-felkod: 0x803381B9 "kan inträffa om Azure DNS tjänsten för installationen inte kan matcha klustret eller värd namnet som du har angett.

- Om du ser det här felet i klustret, FQDN.
- Du kan också se det här felet för värdar i ett kluster. Detta anger att enheten kan ansluta till klustret, men klustret returnerar värdnamn som inte är FQDN. Lös problemet genom att uppdatera hosts-filen på-enheten genom att lägga till en mappning av IP-adress och värdnamn:
    1. Öppna anteckningar som administratör.
    2. Öppna filen C:\Windows\System32\Drivers\etc\hosts.
    3. Lägg till IP-adressen och värd namnet i en rad. Upprepa detta för varje värd eller kluster där du ser det här felet.
    4. Spara och stäng värdfilen.
    5. Kontrol lera om enheten kan ansluta till värdarna med appen för hantering av appar. Efter 30 minuter bör du se den senaste informationen för de här värdarna i Azure Portal.

## <a name="discovered-vms-not-in-portal"></a>Identifierade virtuella datorer som inte är i portalen

Om identifierings status är "identifiering pågår", men ännu inte ser de virtuella datorerna i portalen, väntar du några minuter:
- Det tar ungefär 15 minuter för en virtuell VMware-dator.
- Det tar ungefär två minuter för varje tillagd värd för identifiering av virtuella Hyper-V-datorer.

Om du väntar och statusen inte ändras väljer du **Uppdatera** på fliken **servrar** . Detta ska visa antalet identifierade servrar i Azure Migrate: Server utvärdering och Azure Migrate: Server-migrering.

Om detta inte fungerar och du identifierar VMware-servrar:

- Kontrol lera att det vCenter-konto som du har angett har behörigheten korrekt konfigurerad, med åtkomst till minst en virtuell dator.
- Azure Migrate kan inte identifiera virtuella VMware-datorer om vCenter-kontot har åtkomst beviljad på nivån vCenter VM Folder. [Läs mer](set-discovery-scope.md) om omfattnings identifiering.

## <a name="vm-data-not-in-portal"></a>VM-data som inte ingår i portalen

Om identifierade virtuella datorer inte visas i portalen eller om VM-data är inaktuella, väntar du några minuter. Det tar upp till 30 minuter för ändringar i identifierade VM-konfigurationsdata som visas i portalen. Det kan ta några timmar innan ändringar i program data visas. Om det inte finns några data efter den här tiden kan du prova att uppdatera enligt följande

1. I **servrar** > **Azure Migrate Server utvärdering**väljer du **Översikt**.
2. Under **Hantera**väljer du **agenthälsa**.
3. Välj **Uppdatera agent**.
4. Vänta tills uppdaterings åtgärden har slutförts. Nu bör du se uppdaterad information.

## <a name="deleted-vms-appear-in-portal"></a>Borttagna virtuella datorer visas i portalen

Om du tar bort virtuella datorer och de fortfarande visas i portalen väntar du i 30 minuter. Om de fortfarande visas uppdaterar du enligt beskrivningen ovan.

## <a name="common-app-discovery-errors"></a>Vanliga fel vid identifiering av appar

Azure Migrate stöder identifiering av program, roller och funktioner med hjälp av Azure Migrate: Server utvärdering. Identifiering av appar stöds för närvarande endast för VMware. [Läs mer](how-to-discover-applications.md) om kraven och stegen för att konfigurera identifiering av appar.

Vanliga fel för identifiering av appar sammanfattas i tabellen. 

**Fel** | **Orsak** | **Åtgärd**
--- | --- | --- | ---
10000: "Det gick inte att identifiera de program som är installerade på servern". | Detta kan inträffa om datorns operativ system inte är Windows eller Linux. | Använd endast app Discovery för Windows/Linux.
10001: "Det gick inte att hämta de program som har installerats på servern". | Internt fel-vissa saknade filer i installationen. | Kontakta Microsoft-supporten.
10002: "Det gick inte att hämta de program som har installerats på servern". | Identifierings agenten på enheten kanske inte fungerar korrekt. | Kontakta supporten om problemet inte löses inom 24 timmar.
10003 "Det gick inte att hämta de program som har installerats på servern". | Identifierings agenten på enheten kanske inte fungerar korrekt. | Kontakta supporten om problemet inte löses inom 24 timmar.
10004: "Det gick inte att identifiera installerade program för <Windows/Linux>-datorer." |  Autentiseringsuppgifter för åtkomst till <Windows/Linux> datorer som inte tillhandahölls i installationen.| Lägg till en autentiseringsuppgift till den installation som har åtkomst till <Windows/Linux>-datorer.
10005: det gick inte att komma åt den lokala servern. | Autentiseringsuppgifterna för åtkomsten kan vara fel. | Uppdatera autentiseringsuppgifterna för installationen se till att du har åtkomst till den relevanta datorn med dem. 
10006: det gick inte att komma åt den lokala servern. | Detta kan inträffa om datorns operativ system inte är Windows eller Linux.|  Använd endast app Discovery för Windows/Linux.
10007: "Det gick inte att bearbeta hämtade metadata" | Det uppstod ett internt fel vid försök att deserialisera JSON | Kontakta Microsoft Support för en lösning
9000/9001/9002: "Det gick inte att identifiera de program som är installerade på servern". | VMware-verktyg kanske inte har installerats eller är skadade. | Installera/installera om VMware-verktyg på den aktuella datorn och kontrol lera att den körs.
9003: det gick inte att identifiera de program som är installerade på servern. | Detta kan inträffa om datorns operativ system inte är Windows eller Linux. | Använd endast app Discovery för Windows/Linux.
9004: "Det gick inte att identifiera de program som är installerade på servern". | Detta kan inträffa om den virtuella datorn är avstängd. | För identifiering kontrollerar du att den virtuella datorn finns på.
9005: "Det gick inte att identifiera de program som är installerade på den virtuella datorn. | Detta kan inträffa om datorns operativ system inte är Windows eller Linux. | Använd endast app Discovery för Windows/Linux.
9006/9007: "Det gick inte att hämta de program som har installerats på servern". | Identifierings agenten på enheten kanske inte fungerar korrekt. | Kontakta supporten om problemet inte löses inom 24 timmar.
9008: "Det gick inte att hämta de program som har installerats på servern". | Kan vara ett internt fel.  | TF problemet löses inte inom 24 timmar, kontakta supporten.
9009: "Det gick inte att hämta de program som har installerats på servern". | Kan inträffa om inställningarna för Windows User Account Control (UAC) på servern är restriktiva och förhindra identifiering av installerade program. | Sök efter "User Account Control"-inställningar på servern och konfigurera UAC-inställningen på servern till en av de lägre två nivåerna.
9010: "Det gick inte att hämta de program som har installerats på servern". | Kan vara ett internt fel.  | TF problemet löses inte inom 24 timmar, kontakta supporten.
9011: "Det gick inte att hitta filen som ska laddas ned från gästen på den virtuella gäst datorn" | Problemet kan uppstå på grund av ett internt fel. | Problemet bör automatiskt lösas på 24 timmar. Om problemet kvarstår kontaktar du Microsoft Support.
9012: innehållet i resultat filen är tomt. | Problemet kan uppstå på grund av ett internt fel. | Problemet bör automatiskt lösas på 24 timmar. Om problemet kvarstår kontaktar du Microsoft Support.
9013: "en ny tillfällig profil skapas för varje inloggning till den virtuella VMware-datorn" | En ny tillfällig profil skapas för varje inloggning i den virtuella datorn | Se till att det användar namn som anges i gäst-VM-autentiseringsuppgifterna är i UPN-format.
9015: "det går inte att ansluta till virtuella VMware-datorer på grund av otillräcklig behörighet på vCenter" | Gäst drifts rollen är inte aktive rad på vCenter-användarkontot | Se till att rollen gäst åtgärder är aktive rad på vCenter-användarkontot.
9016: "det går inte att ansluta till virtuella VMware-datorer eftersom gäst åtgärds agenten saknar data" | VMware-verktygen har inte installerats korrekt eller är inte aktuella. | Se till att VMware-verktygen är korrekt installerade och aktuella.
9017: "Det gick inte att hitta filen med identifierade metadata på den virtuella datorn." | Problemet kan uppstå på grund av ett internt fel. | Kontakta Microsoft Support för att få en lösning.
9018: PowerShell är inte installerat på de virtuella gäst datorerna. | PowerShell är inte tillgängligt på den virtuella gäst datorn. | Installera PowerShell på den virtuella gäst datorn.
9019: "Det gick inte att identifiera på grund av problem med den virtuella gäst datorn" | Det gick inte att utföra VMware-gästen på den virtuella datorn. | Se till att autentiseringsuppgifterna för den virtuella datorn är giltiga och att användar namnet som angavs i autentiseringsuppgifterna för gäst-VM är i UPN-format.
9020: "behörighet för att skapa fil nekas". | Rollen som är associerad med användaren eller grup principen begränsar användaren för att skapa filen i mappen | Kontrol lera att den angivna gäst användaren har behörigheten Skapa för filen i mappen. Se **meddelanden** i Server utvärderingen för mappens namn.
9021: "fil skapande behörighet nekas i Temp-sökvägen för temp." | VMware-verktygets version på den virtuella datorn stöds inte | Uppgradera VMware-verktygets version över 10.2.0.
9022: det går inte att få åtkomst till WMI-objekt. | Rollen som är associerad med användaren eller grup principen begränsar användaren för att få åtkomst till WMI-objektet. | Kontakta Microsoft Support.
9023: "systemroots miljö variabel värde är tomt". | Okänd | Kontakta Microsoft Support.
9024: "TEMP-Miljövariabelns värde är tomt". | Okänd | Kontakta Microsoft Support.
9025: PowerShell är skadat i de virtuella gäst datorerna. | Okänd | Installera om PowerShell på den virtuella gäst datorn och kontrol lera om PowerShell kan köras på den virtuella gäst datorn.
8084: "Det gick inte att identifiera program på grund av <Exception from VMware>VMware-fel:" | Azure Migrates enheten använder VMware API: er för att identifiera program. Det här problemet kan inträffa om ett undantags fel uppstår i vCenter Server vid försök att identifiera program. Fel meddelandet från VMware visas i fel meddelandet som visas i portalen. | Sök efter meddelandet i VMware- [dokumentationen](https://pubs.vmware.com/vsphere-51/topic/com.vmware.wssdk.apiref.doc/index-faults.html)och följ stegen för att åtgärda problemet. Kontakta Microsoft-supporten om du inte kan åtgärda det.



## <a name="next-steps"></a>Nästa steg
Konfigurera en installation för [VMware](how-to-set-up-appliance-vmware.md), [Hyper-V](how-to-set-up-appliance-hyper-v.md)eller [fysiska servrar](how-to-set-up-appliance-physical.md).
