---
title: Felsöka distribution och identifiering av Azure Migrate-installationer
description: Få hjälp med att distribuera Azure Migrate-installationen och upptäcka datorer.
author: musa-57
ms.manager: abhemraj
ms.author: hamusa
ms.topic: troubleshooting
ms.date: 01/02/2020
ms.openlocfilehash: 6cb83a87f2e96eb62696e5d92095ef2b8d7c7def
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81677325"
---
# <a name="troubleshoot-the-azure-migrate-appliance-and-discovery"></a>Felsöka Azure Migrate-installationen och identifieringen

Den här artikeln hjälper dig att felsöka problem när du distribuerar [Azure Migrate-enheten](migrate-services-overview.md) och använder den användaren för att identifiera lokala datorer.


## <a name="whats-supported"></a>Vad stöds?

[Läs](migrate-appliance.md) kraven på apparatens support.


## <a name="invalid-ovf-manifest-entry"></a>"Ogiltig OVF-manifestpost"

Om felet "Den angivna manifestfilen är ogiltig: Ogiltig OVF-manifestpost" gör du följande:

1. Kontrollera att OVA-filen Azure Migrate appliance hämtas korrekt genom att kontrollera dess hash-värde. [Läs mer](https://docs.microsoft.com/azure/migrate/tutorial-assessment-vmware). Om hash-värdet inte matchar hämtar du OVA-filen igen och försöker igen distributionen.
2. Om distributionen fortfarande misslyckas och du använder VMware vSphere-klienten för att distribuera OVF-filen kan du prova att distribuera den via vSphere-webbklienten. Om distributionen fortfarande misslyckas kan du prova att använda en annan webbläsare.
3. Om du använder vSphere-webbklienten och försöker distribuera den på vCenter Server 6.5 eller 6.7 kan du försöka distribuera OVA direkt på ESXi-värden:
   - Anslut till ESXi-värden direkt (i stället för vCenter Server) med webbklienten (https://<*värd för IP-adress*>/ui).
   - Välj**Filutbilda OVF-mall** **File** > **i Hemupplag.** **Home** >  Bläddra till OVA och slutför distributionen.
4. Om distributionen fortfarande misslyckas kontaktar du Azure Migrate-supporten.

## <a name="cant-connect-to-the-internet"></a>Det går inte att ansluta till internet

Detta kan inträffa om apparaten maskinen är bakom en proxy.

- Se till att du anger behörighetsautentiseringsuppgifterna om proxyn behöver dem.
- Om du använder en URL-baserad brandväggsproxy för att styra utgående anslutning lägger du till [dessa url:er](migrate-appliance.md#url-access) i en tillåt-lista.
- Om du använder en avlyssningsproxy för att ansluta till Internet importerar du proxycertifikatet till den virtuella datorn med hjälp av [dessa steg](https://docs.microsoft.com/azure/migrate/concepts-collector).

## <a name="cant-sign-into-azure-from-the-appliance-web-app"></a>Det går inte att logga in på Azure från appen förinstallationswebben

Felet "Tyvärr, men vi har problem med att logga in dig" visas om du använder det felaktiga Azure-kontot för att logga in på Azure. Det här felet uppstår av ett par skäl:

- Om du loggar in på programmet för installationsprogrammet för det offentliga molnet använder du användarkontoautentiseringsuppgifter för molnportalen Government.
- Om du loggar in på programmet för apparat för myndighetsmolnet med hjälp av användarkontouppgifter för den privata molnportalen.

Kontrollera att du använder rätt autentiseringsuppgifter.

##  <a name="datetime-synchronization-error"></a>Synkroniseringsfel för datum/tid

Ett fel om datum- och tidssynkronisering (802) indikerar att serverklockan kan vara ur synkronisering med aktuell tid med mer än fem minuter. Ändra klocktiden på den virtuella insamlaren så att den matchar den aktuella tiden:

1. Öppna en kommandotolk för administratörer på den virtuella datorn.
2. Om du vill kontrollera tidszonen kör **du w32tm /tz**.
3. Om du vill synkronisera tiden kör **du w32tm /resync**.


## <a name="unabletoconnecttoserver"></a>"Det går inte att ansluta tillservern"

Om du får det här anslutningsfelet kanske du inte kan ansluta till vCenter Server *Servername*.com:9443. Felinformationen anger att det inte finns `https://\*servername*.com:9443/sdk` någon slutpunktslysning som kan acceptera meddelandet.

- Kontrollera om du kör den senaste versionen av apparaten. Om du inte är det uppgraderar du apparaten till den [senaste versionen](https://docs.microsoft.com/azure/migrate/concepts-collector).
- Om problemet kvarstår i den senaste versionen kanske det inte går att lösa det angivna vCenter Server-namnet eller så kan den angivna porten vara fel. Om porten inte har angetts försöker insamlaren som standard ansluta till portnummer 443.

    1. Ping *Servername*.com från apparaten.
    2. Om steg 1 misslyckas försöker du ansluta till vCenter-servern med IP-adressen.
    3. Identifiera rätt portnummer för anslutning till vCenter Server.
    4. Kontrollera att vCenter Server är igång.


## <a name="error-6005260039-appliance-might-not-be-registered"></a>Fel 60052/60039: Apparaten kanske inte är registrerad

- Fel 60052, "Installationen kanske inte har registrerats i Azure Migrate-projektet" inträffar om Azure-kontot som används för att registrera installationen inte har tillräckliga behörigheter.
    - Kontrollera att det Azure-användarkonto som används för att registrera installationen har minst deltagarbehörighet för prenumerationen.
    - [Läs mer](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance---vmware) om nödvändiga Azure-roller och behörigheter.
- Fel 60039, "Installationen kanske inte har registrerats i Azure Migrate-projektet" kan inträffa om registreringen misslyckas eftersom Azure Migrate-projektet som används för att registrera installationen inte kan hittas.
    - I Azure-portalen och kontrollera om projektet finns i resursgruppen.
    - Om projektet inte finns skapar du ett nytt Azure Migrate-projekt i resursgruppen och registrerar enheten igen. [Läs om hur du](https://docs.microsoft.com/azure/migrate/how-to-add-tool-first-time#create-a-project-and-add-a-tool) skapar ett nytt projekt.

## <a name="error-6003060031-key-vault-management-operation-failed"></a>Fel 60030/60031: Hantering av nyckelvalv misslyckades

Om felet 60030 eller 60031, "En Azure Key Vault-hanteringsåtgärd misslyckades", gör du följande:
- Kontrollera att det Azure-användarkonto som används för att registrera installationen har minst deltagarbehörighet för prenumerationen.
- Kontrollera att kontot har åtkomst till nyckelvalvet som anges i felmeddelandet och försök sedan igen.
- Kontakta Microsofts supportavdelning om problemet kvarstår.
- [Läs mer](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance---vmware) om de nödvändiga Azure-roller och behörigheter.

## <a name="error-60028-discovery-couldnt-be-initiated"></a>Fel 60028: Identifieringen kunde inte initieras

Fel 60028: "Identifiering kunde inte initieras på grund av ett fel. Åtgärden misslyckades för den angivna listan över värdar eller kluster" anger att identifieringen inte kunde startas på värdarna som anges i felet på grund av ett problem med att komma åt eller hämta VM-information. Resten av värdarna har lagts till.

- Lägg till värdarna som visas i felet igen med alternativet **Lägg till värd.**
- Om det finns ett valideringsfel läser du reparationsvägledningen för att åtgärda felen och försöker sedan använda alternativet **Spara och starta identifiering** igen.

## <a name="error-60025-azure-ad-operation-failed"></a>Fel 60025: Azure AD-åtgärden misslyckades 
Fel 60025: "En Azure AD-åtgärd misslyckades. Felet uppstod när Azure AD-programmet skulle skapas eller uppdateras" uppstår när Azure-användarkontot som används för att initiera identifieringen skiljer sig från det konto som används för att registrera installationen. Gör något av följande:

- Kontrollera att användarkontot som initierar identifieringen är samma som det som används för att registrera apparaten.
- Ange Azure Active Directory-programåtkomstbehörigheter till användarkontot som identifieringsåtgärden misslyckas med.
- Ta bort resursgruppen som tidigare skapats för Azure Migrate-projektet. Skapa en annan resursgrupp för att starta igen.
- [Läs mer](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance---vmware) om Azure Active Directory-programbehörigheter.


## <a name="error-50004-cant-connect-to-host-or-cluster"></a>Fel 50004: Det går inte att ansluta till värd- eller kluster

Fel 50004: "Det går inte att ansluta till en värd eller ett kluster eftersom servernamnet inte kan matchas. WinRM-felkod: 0x803381B9" kan uppstå om Azure DNS-tjänsten för installationen inte kan lösa klustret eller värdnamnet som du angav.

- Om det här felet visas i klustret finns FQDN i klustret.
- Du kan också se det här felet för värdar i ett kluster. Detta indikerar att enheten kan ansluta till klustret, men klustret returnerar värdnamn som inte är FQDN.This indicates that the appliance can connect to the cluster, but the cluster returns host names that aren't FQDNs. LÃ¶s problemet genom att uppdatera hosts-filen fÃ¶r installationen genom att lägga till en mappning av IP-adressen och värdnamnen:
    1. Öppna Anteckningar som administratör.
    2. Öppna filen C:\Windows\System32\Drivers\etc\hosts.
    3. Lägg till IP-adressen och värdnamnet på en rad. Upprepa för varje värd eller kluster där du ser det här felet.
    4. Spara och stäng värdfilen.
    5. Kontrollera om apparaten kan ansluta till värdarna med hjälp av appen för hantering av apparaten. Efter 30 minuter bör du se den senaste informationen för dessa värdar i Azure-portalen.

## <a name="discovered-vms-not-in-portal"></a>Identifierade virtuella datorer som inte finns i portalen

Om identifieringstillståndet är "Discovery in progress", men ännu inte ser de virtuella datorerna i portalen, väntar några minuter:
- Det tar cirka 15 minuter för en VMware VM.
- Det tar cirka två minuter för varje tillagd värd för Hyper-V VM-identifiering.

Om du väntar och tillståndet inte ändras väljer du **Uppdatera** på fliken **Servrar.** Detta bör visa antalet identifierade servrar i Azure Migrate: Server Assessment och Azure Migrate: ServerMigrering.

Om detta inte fungerar och du upptäcker VMware-servrar:

- Kontrollera att det vCenter-konto som du angav har behörigheter som ställts in korrekt, med åtkomst till minst en virtuell dator.
- Azure Migrate kan inte identifieras virtuella VMware-datorer om vCenter-kontot har åtkomst beviljad på mappnivå för vCenter VM. [Läs mer](set-discovery-scope.md) om att omfånga upptäckt.

## <a name="vm-data-not-in-portal"></a>VM-data som inte finns i portalen

Om identifierade virtuella datorer inte visas i portalen eller om vm-data är inaktuella väntar du några minuter. Det tar upp till 30 minuter innan ändringar i identifierade VM-konfigurationsdata visas i portalen. Det kan ta några timmar innan ändringar i programdata visas. Om det inte finns några data efter den här tiden kan du prova att uppdatera, enligt följande

1. Välj **Översikt**i **Servrar** > **Azure Migrera serverutvärdering**.
2. Under **Hantera**väljer du **Agenthälsa**.
3. Välj **Uppdatera agent**.
4. Vänta tills uppdateringsåtgärden är klar. Du bör nu se aktuell information.

## <a name="deleted-vms-appear-in-portal"></a>Borttagna virtuella datorer visas i portalen

Om du tar bort virtuella datorer och de fortfarande visas i portalen väntar du i 30 minuter. Om de fortfarande visas uppdateras du enligt beskrivningen ovan.

## <a name="common-app-discovery-errors"></a>Vanliga fel för identifiering av appar

Azure Migrate stöder identifiering av program, roller och funktioner med hjälp av Azure Migrate: Server Assessment. Appidentifiering stöds för närvarande endast för VMware. [Läs mer](how-to-discover-applications.md) om kraven och stegen för att konfigurera appidentifiering.

Typiska appidentifieringsfel sammanfattas i tabellen. 

**Fel** | **Orsak** | **Åtgärd**
--- | --- | --- | ---
10000: "Det går inte att identifiera de program som är installerade på servern". | Detta kan inträffa om datorns operativsystem inte är Windows eller Linux. | Använd endast appidentifiering för Windows/Linux.
10001: "Det gick inte att hämta programmen som installerat servern". | Internt fel - vissa filer som saknas i installationen. | Kontakta Microsoft-supporten.
10002: "Det gick inte att hämta programmen som installerat servern". | Identifieringsagenten på apparaten kanske inte fungerar som den ska. | Om problemet inte löser sig inom 24 timmar kontaktar du supporten.
10003 "Det gick inte att hämta programmen installerade servern". | Identifieringsagenten på apparaten kanske inte fungerar som den ska. | Om problemet inte löser sig inom 24 timmar kontaktar du supporten.
10004: "Det går inte att identifiera installerade program för <Windows/Linux->-datorer." |  Autentiseringsuppgifter för åtkomst <Windows/Linux-> maskiner fanns inte i apparaten.| Lägg till en autentiseringsåtkomst till den apparat som har åtkomst till <Windows/Linux->-datorer.
10005: "Det går inte att komma åt den lokala servern". | Åtkomstautentiseringsuppgifterna kan vara felaktiga. | Uppdatera autentiseringsuppgifterna för apparaten se till att du kan komma åt den aktuella datorn med dem. 
10006: "Det går inte att komma åt den lokala servern". | Detta kan inträffa om datorns operativsystem inte är Windows eller Linux.|  Använd endast appidentifiering för Windows/Linux.
10007: "Det går inte att bearbeta metadata som hämtats" | Det här interna felet uppstod när JSON försökte avserialiseras | Kontakta Microsoft Support för en lösning
9000/9001/9002: "Det går inte att identifiera de program som är installerade på servern". | VMware-verktyg kanske inte är installerade eller är skadade. | Installera/installera om VMware-verktyg på den aktuella datorn och kontrollera att den körs.
9003: Det gick inte att identifiera de program som är installerade på servern". | Detta kan inträffa om datorns operativsystem inte är Windows eller Linux. | Använd endast appidentifiering för Windows/Linux.
9004: "Det går inte att identifiera de program som är installerade på servern". | Detta kan inträffa om den virtuella datorn är avstängd. | Kontrollera att den virtuella datorn är aktiverad för identifiering.
9005: "Det gick inte att identifiera de program som är installerade på den virtuella datorn. | Detta kan inträffa om datorns operativsystem inte är Windows eller Linux. | Använd endast appidentifiering för Windows/Linux.
9006/9007: "Det gick inte att hämta programmen som installerat servern". | Identifieringsagenten på apparaten kanske inte fungerar som den ska. | Om problemet inte löser sig inom 24 timmar kontaktar du supporten.
9008: "Det gick inte att hämta programmen som installerat servern". | Kan vara ett internt fel.  | Tf problemet löser sig inte inom 24 timmar, kontakta support.
9009: "Det gick inte att hämta programmen som installerat servern". | Kan uppstå om UAC-inställningarna (Windows User Account Control) på servern är restriktiva och förhindrar identifiering av installerade program. | Sök efter inställningar för användarkontokontroll på servern och konfigurera UAC-inställningen på servern till en av de två lägre nivåerna.
9010: "Det gick inte att hämta programmen som installerat servern". | Kan vara ett internt fel.  | Tf problemet löser sig inte inom 24 timmar, kontakta support.
9011: "Fil att ladda ner från gästen finns inte på gäst-VM" | Problemet kan uppstå på grund av ett internt fel. | Problemet bör automatiskt lösas inom 24 timmar. Om problemet kvarstår kontaktar du Microsoft Support.
9012: "Resultatet filinnehållet är tomt." | Problemet kan uppstå på grund av ett internt fel. | Problemet bör automatiskt lösas inom 24 timmar. Om problemet kvarstår kontaktar du Microsoft Support.
9013: "En ny tillfällig profil skapas för varje inloggning till VMware-VM" | En ny tillfällig profil skapas för varje inloggning till den virtuella datorn | Kontrollera att användarnamnet som anges i autentiseringsuppgifterna för den virtuella gästen är i UPN-format.
9015: "Det går inte att ansluta till virtuella datorer med VMware på grund av otillräckliga privilegier på vCenter" | Rollen Gästoperationer är inte aktiverad på vCenter-användarkontot | Kontrollera att gästoperationsrollen är aktiverad på vCenter-användarkontot.
9016: Det går inte att ansluta till virtuella datorer med VMware eftersom gästoperationsagenten har på data | VMware-verktygen är inte korrekt installerade eller är inte uppdaterade. | Se till att VMware-verktygen är korrekt installerade och uppdaterade.
9017: "Filen med identifierade metadata finns inte på den virtuella datorn." | Problemet kan uppstå på grund av ett internt fel. | Kontakta Microsoft Support för en lösning.
9018: "PowerShell är inte installerat i virtuella gästdpm:er." | PowerShell är inte tillgängligt i gästdatorn. | Installera PowerShell i gästdatorn.
9019: "Det går inte att upptäcka på grund av gäst-VM-driftfel" | VMware-gäståtgärden misslyckades på den virtuella datorn. | Kontrollera att autentiseringsuppgifterna för den virtuella datorn är giltiga och att användarnamnet som anges i autentiseringsuppgifterna för den virtuella datorn är i UPN-format.
9020: "Behörighet för skapande av filer nekas." | Rollen som är associerad med användaren eller grupprincipen begränsar användaren att skapa filen i mappen | Kontrollera om gästanvändaren som har skapat behörighet för filen i mappen. Se **Meddelanden** i Serverutvärdering för namnet på mappen.
9021: "Behörighet för filskapning nekas i mappen Systemtemp Path." | VMware-verktygsversionen på den virtuella datorn stöds inte | Uppgradera din VMware verktygsversion ovan 10.2.0.
9022: "Få åtkomst till WMI-objekt nekas." | Rollen som är associerad med användaren eller grupprincipen begränsar användaren att komma åt WMI-objekt. | Kontakta Microsoft Support.
9023: "SystemRoot-miljövariabelvärdet är tomt." | Ingen känd | Kontakta Microsoft Support.
9024: "VARIABELT VÄRDE FÖR TEMP-miljön är tomt." | Ingen känd | Kontakta Microsoft Support.
9025: "PowerShell är skadat i de virtuella gästspelarna." | Ingen känd | Installera om PowerShell i gästdatorn och kontrollera om PowerShell kan köras på gästdatorn.
8084: "Det går inte att upptäcka program <Exception from VMware>på grund av VMware fel: " | Azure Migrate-installationen använder VMware API:er för att identifiera program. Det här problemet kan inträffa om ett undantag genereras av vCenter Server när du försöker identifiera program. Felmeddelandet från VMware visas i felmeddelandet som visas i portalen. | Sök efter meddelandet i [VMware-dokumentationen](https://pubs.vmware.com/vsphere-51/topic/com.vmware.wssdk.apiref.doc/index-faults.html)och följ stegen för att åtgärda. Om du inte kan åtgärda det kontaktar du Microsoft-supporten.



## <a name="next-steps"></a>Nästa steg
Konfigurera en apparat för [VMware,](how-to-set-up-appliance-vmware.md) [Hyper-V](how-to-set-up-appliance-hyper-v.md)eller [fysiska servrar](how-to-set-up-appliance-physical.md).
