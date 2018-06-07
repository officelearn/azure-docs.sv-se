---
title: Azure AD Connect - LargeObject fel som orsakats av userCertificate attributet | Microsoft Docs
description: Det här avsnittet innehåller reparationssteg för LargeObject fel som orsakats av userCertificate attribut.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 146ad5b3-74d9-4a83-b9e8-0973a19828d9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.component: hybrid
ms.author: billmath
ms.custom: seohack1
ms.openlocfilehash: 9866454735b33239a812dca238006299c74e5ae2
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34592814"
---
# <a name="azure-ad-connect-sync-handling-largeobject-errors-caused-by-usercertificate-attribute"></a>Azure AD Connect-synkronisering: hantera LargeObject fel som orsakats av userCertificate attribut

Azure AD tillämpar övre gräns **15** certifikat värden på den **userCertificate** attribut. Om Azure AD Connect exporterar ett objekt med mer än 15 värden till Azure AD, Azure AD returnerar en **LargeObject** fel med meddelandet:

>*”Objektet är för stor. Begränsa antalet attributvärden för det här objektet. Åtgärden upprepas under nästa synkroniseringscykel... ”*

LargeObject felet kan orsakas av andra AD-attribut. För att bekräfta att det verkligen beror på attributet userCertificate, måste du verifiera mot antingen i lokala AD-objekt eller i den [Synchronization Service Manager metaversumsökningen](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-service-manager-ui-mvsearch).

Använd någon av följande metoder för att hämta listan med objekt i din klient med LargeObject fel:

 * Om din klient är aktiverad för Azure AD Connect Health för synkronisering kan du referera till den [synkronisering felrapporten](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health-sync#object-level-synchronization-error-report-preview) tillhandahålls.
 
 * E-postmeddelandet för directory synkroniseringsfel som skickas i slutet av varje cykel för synkronisering med en lista över objekt med LargeObject fel. 
 * Den [Synchronization Service Manager Operations fliken](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-service-manager-ui-operations) visar en lista med objekt med LargeObject fel om du klickar på den senaste exporten till Azure AD-åtgärden.
 
## <a name="mitigation-options"></a>Alternativ för lösning
Andra attributändringar till samma objekt kan inte exporteras till Azure AD förrän LargeObject felet är löst. Du kan lösa problemet kan du följande alternativ:

 * Uppgradera Azure AD Connect för att skapa 1.1.524.0 eller efter. Skapa 1.1.524.0, out of box synkroniseringen regler har uppdaterats för att inte exportera attribut userCertificate och userSMIMECertificate om attribut som har mer än 15 värden i Azure AD Connect. Mer information om hur du uppgraderar Azure AD Connect finns i artikel [Azure AD Connect: uppgradera från en tidigare version till senast](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version).

 * Implementera en **utgående synkroniseringsregel** i Azure AD Connect som exporterar en **null-värde i stället för de faktiska värdena för objekt med mer än 15 certifikat värden**. Det här alternativet är lämpligt om du inte behöver något av värdena för certifikat som ska exporteras till Azure AD för objekt med mer än 15 värden. Mer information om hur du implementerar regeln synkronisering finns i nästa avsnitt [implementera synkroniseringsregel för att begränsa export av userCertificate attributet](#implementing-sync-rule-to-limit-export-of-usercertificate-attribute).

 * Minska antalet certifikat värden på lokalt AD-objekt (15 eller mindre) genom att ta bort värden som inte längre används av din organisation. Detta är lämpligt om attributet överdriven-storlek orsakas av certifikat som har upphört att gälla eller används inte. Du kan använda den [PowerShell-skript finns här](https://gallery.technet.microsoft.com/Remove-Expired-Certificates-0517e34f) säkerhetskopiering för att hitta och ta bort utgångna certifikat i din lokala AD. Innan du tar bort certifikat, bör du kontrollera med infrastruktur för offentliga nycklar administratörer i din organisation.

 * Konfigurera Azure AD Connect om du vill exkludera attributet userCertificate exporteras till Azure AD. I allmänhet rekommenderas inte det här alternativet eftersom attributet kan användas av Microsoft Online Services för att möjliggöra specifika scenarier. I synnerhet:
    * Attributet userCertificate användarobjektet används av Exchange Online och Outlook-klienter för Meddelandesignering och kryptering. Mer information om den här funktionen finns i artikel [S/MIME för Meddelandesignering och kryptering](https://technet.microsoft.com/library/dn626158(v=exchg.150).aspx).

    * Attributet userCertificate på datorobjektet används av Azure AD så att Windows 10 lokala domänanslutna enheter att ansluta till Azure AD. Mer information om den här funktionen finns i artikel [ansluta domänanslutna enheter till Azure AD för Windows 10-upplevelser](https://docs.microsoft.com/azure/active-directory/active-directory-azureadjoin-devices-group-policy).

## <a name="implementing-sync-rule-to-limit-export-of-usercertificate-attribute"></a>Implementera synkroniseringsregel för att begränsa export av userCertificate attribut
Lös LargeObject orsakas felet av attributet userCertificate, du kan implementera en synkroniseringsregel för utgående i Azure AD Connect som exporterar en **null-värde i stället för de faktiska värdena för objekt med mer än 15 certifikat värden**. Det här avsnittet beskriver de steg som krävs för att implementera synkroniseringsregel för **användaren** objekt. Steg som kan anpassas för **Kontakta** och **datorn** objekt.

> [!IMPORTANT]
> Exportera nullvärde tar bort certifikatet värden som tidigare har exporterats till Azure AD.

Stegen kan sammanfattas som:

1. Inaktivera sync scheduler och kontrollera att det finns ingen synkronisering pågår.
3. Hitta befintliga utgående synkroniseringsregel för userCertificate attribut.
4. Skapa synkroniseringsregel för utgående som krävs.
5. Kontrollera den nya sync-regeln på ett befintligt objekt med LargeObject fel.
6. Tillämpa nya sync-regel för kvarvarande objekt med LargeObject fel.
7. Kontrollera att det inte finns några oväntade ändringar som väntar på att exporteras till Azure AD.
8. Exportera ändringarna till Azure AD.
9. Återaktivera sync scheduler.

### <a name="step-1-disable-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>Steg 1. Inaktivera sync scheduler och kontrollera att det finns ingen synkronisering pågår
Kontrollera sker ingen synkronisering när du arbetar på att implementera en ny synkroniseringsregel för att undvika oväntade ändringar exporteras till Azure AD. Inaktivera inbyggda sync scheduler:
1. Starta PowerShell-session på Azure AD Connect-servern.

2. Inaktivera schemalagda synkroniseringen genom att köra cmdlet: `Set-ADSyncScheduler -SyncCycleEnabled $false`

> [!Note]
> Ovanstående gäller endast för nyare versioner (1.1.xxx.x) av Azure AD Connect med inbyggda Schemaläggaren. Om du använder äldre versioner (1.0.xxx.x) av Azure AD Connect som använder Schemaläggaren i Windows, eller om du använder egna anpassade scheduler (inte vanliga) för att utlösa återkommande synkronisering, måste du inaktivera dem i enlighet med detta.

3. Starta den **Synchronization Service Manager** genom att gå till START → synkroniseringstjänsten.

4. Gå till den **Operations** fliken och bekräfta att ingen åtgärd vars status är *”pågår”.*

### <a name="step-2-find-the-existing-outbound-sync-rule-for-usercertificate-attribute"></a>Steg 2. Hitta befintliga utgående synkroniseringsregel för userCertificate attribut
Det bör finnas en befintlig sync-regel som är aktiverad och konfigurerad för att exportera userCertificate attribut för användarobjekt till Azure AD. Leta upp regeln sync för att ta reda på dess **prioritet** och **målgrupp filter** konfiguration:

1. Starta den **Synchronization regler Editor** genom att gå till START → Synchronization regler Editor.

2. Konfigurera filter för sökningen med följande värden:

    | Attribut | Värde |
    | --- | --- |
    | Riktning |**Utgående** |
    | MV-objekttyp |**Person** |
    | Koppling |*namnet på Azure AD-koppling* |
    | Objekttyp för kopplingen |**Användaren** |
    | MV-attribut |**userCertificate** |

3. Om du använder OOB (out of box) sync regler till Azure AD-koppling för att exportera userCertficiate attribut för användarobjekt du bör få tillbaka det *”ut till AAD – användaren ExchangeOnline”* regeln.
4. Notera den **prioritet** värdet för den här regeln för synkronisering.
5. Välj en synkroniseringsregel för och klicka på **redigera**.
6. I den *”redigera reserverade regeln bekräftelse”* popup-fönstret klickar du på **nr**. (Oroa dig inte, vi kommer inte att göra ändringar i regeln sync).
7. På skärmen redigera väljer den **Scoping filter** fliken.
8. Notera målgrupp filterkonfiguration. Om du använder synkroniseringsregel OOB det exakt ska **en målgrupp filter-grupp som innehåller två satser**, inklusive:

    | Attribut | Operator | Värde |
    | --- | --- | --- |
    | sourceObjectType | LIKA MED | Användare |
    | cloudMastered | NOTEQUAL | True |

### <a name="step-3-create-the-outbound-sync-rule-required"></a>Steg 3. Skapa synkroniseringsregel för utgående krävs
Den nya regeln synkronisering måste ha samma **målgrupp filter** och **högre prioritet** än den befintliga sync-regeln. Detta säkerställer att den nya regeln för synkronisering gäller samma uppsättning objekt som den befintliga sync-regeln och åsidosätter den befintliga sync-regeln för attributet userCertificate. Att skapa synkroniseringsregel för:
1. I redigeraren synkronisering regler klickar du på den **Lägg till ny regel** knappen.
2. Under den **fliken Beskrivning**, ange följande konfiguration:

    | Attribut | Värde | Information |
    | --- | --- | --- |
    | Namn | *Ange ett namn* | T.ex. *”ut till AAD – anpassad åsidosätta för userCertificate”* |
    | Beskrivning | *Ange en beskrivning* | T.ex. *”userCertificate attributet har mer än 15 värden, exportera NULL”.* |
    | Det anslutna systemet | *Välj den Azure AD-koppling* |
    | Anslutna System objekttyp | **Användaren** | |
    | Typ av Metaversumobjekt | **Person** | |
    | Länktypen | **Anslut dig** | |
    | Prioritet | *Om du har valt ett tal mellan 1 och 99* | Det antal valt får inte användas av någon befintlig synkroniseringsregel och har ett lägre värde (och därför högre prioritet) än den befintliga sync-regeln. |

3. Gå till den **Scoping filter** fliken och implementera samma målgrupp filter med hjälp av den befintliga sync-regeln.
4. Hoppa över den **ansluta regler** fliken.
5. Gå till den **transformationer** fliken om du vill lägga till en ny omvandling med följande konfiguration:

    | Attribut | Värde |
    | --- | --- |
    | Flöde |**uttryck** |
    | Målattribut |**userCertificate** |
    | Källattributet |*Använd följande uttryck*: `IIF(IsNullOrEmpty([userCertificate]), NULL, IIF((Count([userCertificate])> 15),AuthoritativeNull,[userCertificate]))` |
    
6. Klicka på den **Lägg till** för att skapa synkroniseringsregel för.

### <a name="step-4-verify-the-new-sync-rule-on-an-existing-object-with-largeobject-error"></a>Steg 4. Kontrollera den nya sync-regeln på ett befintligt objekt med LargeObject fel
Detta är att kontrollera att synkroniseringsregel som skapade fungerar på ett befintligt AD-objekt med LargeObject fel innan du installerar andra objekt:
1. Gå till den **Operations** fliken i hanteraren för synkroniseringstjänsten.
2. Välj den senaste exporten till Azure AD-åtgärden och klicka på ett objekt med LargeObject fel.
3.  I popup-skärmen objektegenskaper för Anslutarplats klickar du på den **Preview** knappen.
4. På skärmen Preview popup väljer **fullständig synkronisering** och på **genomför Preview**.
5. Stäng skärmen förhandsgranskning och objektegenskaper för Anslutarplats skärmen.
6. Gå till den **kopplingar** fliken i hanteraren för synkroniseringstjänsten.
7. Högerklicka på den **Azure AD** koppling och välj **kör...**
8. I popup-fönstret kör koppling, Välj **exportera** steg och på **OK**.
9. Vänta tills exporten till Azure AD för att slutföra och bekräfta att det finns ingen mer LargeObject fel på det markerade objektet.

### <a name="step-5-apply-the-new-sync-rule-to-remaining-objects-with-largeobject-error"></a>Steg 5. Tillämpa den nya regeln för synkronisering till kvarvarande objekt med LargeObject fel
När en synkroniseringsregel för har lagts till, måste du kör en fullständig synkronisering steget AD-anslutningen:
1. Gå till den **kopplingar** fliken i hanteraren för synkroniseringstjänsten.
2. Högerklicka på den **AD** koppling och välj **kör...**
3. I popup-fönstret kör koppling, Välj **fullständig synkronisering** steg och på **OK**.
4. Vänta tills steget fullständig synkronisering ska slutföras.
5. Upprepa ovanstående steg för de återstående AD-kopplingarna om du har flera AD-kopplingar. Vanligtvis krävs flera kopplingar om du har flera lokala kataloger.

### <a name="step-6-verify-there-are-no-unexpected-changes-waiting-to-be-exported-to-azure-ad"></a>Steg 6. Kontrollera att det finns inga oväntade ändringar som väntar på att exporteras till Azure AD
1. Gå till den **kopplingar** fliken i hanteraren för synkroniseringstjänsten.
2. Högerklicka på den **Azure AD** koppling och välj **söka Anslutarplats**.
3. I popup-fönstret Sök anslutningsplatsen:
    1. Ange omfånget till **väntande Export**.
    2. Markera kryssrutorna för alla 3, inklusive **Lägg till**, **ändra** och **ta bort**.
    3. Klicka på **Sök** för att returnera alla objekt med ändringar som väntar på att exporteras till Azure AD.
    4. Kontrollera att inga ändringar har gjorts oväntat. Dubbelklicka på objektet om du vill granska ändringarna för ett angivet objekt.

### <a name="step-7-export-the-changes-to-azure-ad"></a>Steg 7. Exportera ändringarna till Azure AD
Så här exporterar ändringarna till Azure AD:
1. Gå till den **kopplingar** fliken i hanteraren för synkroniseringstjänsten.
2. Högerklicka på den **Azure AD** koppling och välj **kör...**
4. I popup-fönstret kör koppling, Välj **exportera** steg och på **OK**.
5. Vänta tills exporten till Azure AD för att slutföra och bekräfta att det inte finns några fler LargeObject-fel.

### <a name="step-8-re-enable-sync-scheduler"></a>Steg 8. Återaktivera sync scheduler
Nu när problemet är löst återaktivera inbyggda sync scheduler:
1. Starta PowerShell-session.
2. Aktivera schemalagd synkronisering igen genom att köra cmdlet: `Set-ADSyncScheduler -SyncCycleEnabled $true`

> [!Note]
> Ovanstående gäller endast för nyare versioner (1.1.xxx.x) av Azure AD Connect med inbyggda Schemaläggaren. Om du använder äldre versioner (1.0.xxx.x) av Azure AD Connect som använder Schemaläggaren i Windows, eller om du använder egna anpassade scheduler (inte vanliga) för att utlösa återkommande synkronisering, måste du inaktivera dem i enlighet med detta.

## <a name="next-steps"></a>Nästa steg
Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](active-directory-aadconnect.md).

