---
title: Azure AD Connect - LargeObject-fel som orsakats av userCertificate-attributet | Microsoft Docs
description: Det här avsnittet innehåller åtgärdssteg LargeObject-fel som orsakats av userCertificate-attributet.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 146ad5b3-74d9-4a83-b9e8-0973a19828d9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.openlocfilehash: 5361b93d24c66ef6ccb2f117e6e3a68de4c5f459
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55195625"
---
# <a name="azure-ad-connect-sync-handling-largeobject-errors-caused-by-usercertificate-attribute"></a>Azure AD Connect-synkronisering: Hantera LargeObject-fel som orsakats av userCertificate-attributet

Azure AD tillämpar en maxgräns på **15** certifikat värden på den **userCertificate** attribut. Om Azure AD Connect exporterar ett objekt med mer än 15 värden till Azure AD, Azure AD returnerar en **LargeObject** fel med meddelandet:

>*”Objektet är för stor. Begränsa antalet attributvärden för det här objektet. Åtgärden upprepas under nästa synkroniseringscykel... ”*

LargeObject-fel kan ha orsakats av andra AD-attribut. För att bekräfta att det verkligen orsakade av userCertificate-attributet, måste du verifiera mot objektet i lokala AD och i den [hanteraren för synkroniseringstjänsten Metaversumsökning](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-service-manager-ui-mvsearch).

Använd någon av följande metoder för att hämta listan med objekt i din klient med LargeObject-fel:

 * Om din klient har aktiverats för Azure AD Connect Health för synkronisering, kan du referera till den [felrapport för synkronisering](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health-sync#object-level-synchronization-error-report-preview) tillhandahålls.
 
 * E-postmeddelandet för directory synkroniseringsfel som skickas i slutet av varje synkroniseringscykel med en lista över objekt med LargeObject-fel. 
 * Den [Synchronization Service Manager Operations-fliken](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-service-manager-ui-operations) visar en lista över objekt med LargeObject-fel om du klickar på den senaste exporten till Azure AD-åtgärd.
 
## <a name="mitigation-options"></a>Alternativ för lösning
Tills LargeObject-fel har åtgärdats, kan andra attributändringar till samma objekt inte exporteras till Azure AD. Lös felet, kan du överväga följande alternativ:

 * Uppgradera Azure AD Connect för att skapa 1.1.524.0 eller efter. Skapa 1.1.524.0, out-of-box-synkroniseringen regler har uppdaterats för att inte exportera attribut userCertificate- och userSMIMECertificate om attribut har fler än 15 värden i Azure AD Connect. Mer information om hur du uppgraderar Azure AD Connect finns artikeln [Azure AD Connect: Uppgradera från en tidigare version till senast](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version).

 * Implementera en **utgående synkroniseringsregel** i Azure AD Connect som exporterar en **null-värde i stället för de faktiska värdena för objekt med mer än 15 certifikatvärden**. Det här alternativet är lämpligt om du inte behöver något av värdena för certifikat som ska exporteras till Azure AD för objekt med mer än 15 värden. Information om hur du implementerar den här regeln för synkronisering, i nästa avsnitt [implementera synkroniseringsregel för att begränsa export av userCertificate-attributet](#implementing-sync-rule-to-limit-export-of-usercertificate-attribute).

 * Minska antalet certifikatvärden på lokalt AD-objekt (15 eller mindre) genom att ta bort värden som inte längre används av din organisation. Detta är lämpligt om det attributet överdriven storlek beror på har upphört att gälla eller oanvända certifikat. Du kan använda den [PowerShell-skript tillgänglig här](https://gallery.technet.microsoft.com/Remove-Expired-Certificates-0517e34f) för att hitta, säkerhetskopiera och ta bort utgångna certifikat i din lokala AD. Innan du tar bort certifikaten, rekommenderar vi att du verifierar med infrastruktur för offentliga nycklar administratörer i din organisation.

 * Konfigurera Azure AD Connect för att undanta userCertificate-attributet från som exporteras till Azure AD. I allmänhet rekommenderar vi inte det här alternativet eftersom attributet kan användas av Microsoft Online Services för att möjliggöra specifika scenarier. Särskilt:
    * UserCertificate-attributet för User-objektet används av Exchange Online och Outlook-klienter för Meddelandesignering och kryptering. Mer information om den här funktionen finns i artikeln [S/MIME för Meddelandesignering och kryptering](https://technet.microsoft.com/library/dn626158(v=exchg.150).aspx).

    * UserCertificate-attributet på datorobjektet används av Azure AD för att tillåta domänanslutna enheter att ansluta till Azure AD för Windows 10 på plats. Mer information om den här funktionen finns i artikeln [ansluta domänanslutna enheter till Azure AD för Windows 10 inträffar](https://docs.microsoft.com/azure/active-directory/active-directory-azureadjoin-devices-group-policy).

## <a name="implementing-sync-rule-to-limit-export-of-usercertificate-attribute"></a>Implementera synkroniseringsregel för att begränsa export av userCertificate-attributet
För att lösa LargeObject-fel som orsakats av userCertificate-attributet kan du implementera en utgående synkroniseringsregel i Azure AD Connect som exporterar en **null-värde i stället för de faktiska värdena för objekt med mer än 15 certifikatvärden**. Det här avsnittet beskriver de steg som krävs för att implementera synkroniseringsregel för **användaren** objekt. Stegen kan anpassas för **Kontakta** och **datorn** objekt.

> [!IMPORTANT]
> Exportera nullvärde tar bort certifikatet värden som tidigare har exporterats till Azure AD.

Stegen kan sammanfattas som:

1. Inaktivera sync scheduler och kontrollera att det finns ingen synkronisering pågår.
3. Hitta befintliga utgående synkroniseringsregel för userCertificate-attributet.
4. Skapa den utgående synkroniseringsregel som krävs.
5. Kontrollera den nya sync-regeln på ett befintligt objekt med LargeObject-fel.
6. Gäller den nya regeln för synkronisering för kvarvarande objekt med LargeObject-fel.
7. Kontrollera att det finns inga oväntade ändringar som väntar på att exporteras till Azure AD.
8. Exportera ändringarna till Azure AD.
9. Återaktivera synkroniseringsschemaläggaren.

### <a name="step-1-disable-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>Steg 1. Inaktivera sync scheduler och verifiera det finns ingen synkronisering pågår
Se till att ingen synkronisering sker när du är mitt implementerar en ny synkroniseringsregel för att undvika oväntade ändringar exporteras till Azure AD. Inaktivera inbyggda sync scheduler:
1. Starta PowerShell-session på Azure AD Connect-servern.

2. Inaktivera schemalagd synkronisering genom att köra cmdlet: `Set-ADSyncScheduler -SyncCycleEnabled $false`

> [!Note]
> Föregående steg gäller endast för nyare versioner (1.1.xxx.x) av Azure AD Connect med inbyggda scheduler. Om du använder äldre versioner (1.0.xxx.x) av Azure AD Connect som använder Schemaläggaren i Windows, eller om du använder din egen anpassade scheduler (inte vanliga) för att utlösa Periodisk synkronisering, måste du inaktivera dem enlighet med detta.

3. Starta den **hanteraren för synkroniseringstjänsten** genom att gå till START → synkroniseringstjänsten.

4. Gå till den **Operations** fliken och bekräfta att ingen åtgärd har vars status är *”pågår”.*

### <a name="step-2-find-the-existing-outbound-sync-rule-for-usercertificate-attribute"></a>Steg 2. Hitta befintliga utgående synkroniseringsregel för userCertificate-attributet
Det bör finnas en befintlig sync-regel som är aktiverad och konfigurerad för att exportera userCertificate-attributet för användarobjekt till Azure AD. Leta upp den här synkroniseringsregel för att ta reda på dess **prioritet** och **Omfångsfilter** konfiguration:

1. Starta den **Synchronization Rules Editor** genom att gå till START → Synchronization Rules Editor.

2. Konfigurera filter för sökningen med följande värden:

    | Attribut | Värde |
    | --- | --- |
    | Riktning |**Utgående** |
    | MV-objekttyp |**Person** |
    | Koppling |*namnet på din Azure AD-koppling* |
    | Objekttyp för anslutningen |**Användaren** |
    | MV-attribut |**userCertificate** |

3. Om du använder Synkroniseringsregler för OOB (out of box) till Azure AD-koppling för att exportera userCertficiate attribut för användarobjekt du bör få tillbaka den *”ut till AAD – användaren ExchangeOnline”* regeln.
4. Anteckna den **prioritet** värdet för den här regeln för synkronisering.
5. Välj synkronisering av regeln och klicka på **redigera**.
6. I den *”redigera reserverade regeln bekräftelse”* standardarbetsytan i dialogrutan, klickar du på **nr**. (Oroa dig inte, vi ska inte göra ändringar i regeln sync).
7. Redigeringsskärmen väljer du den **Scoping filter** fliken.
8. Anteckna målgrupp filterkonfigurationen. Om du använder OOB-synkroniseringsregel, bör det exakt finnas **en gemensam Filtergrupp som innehåller två satser**, inklusive:

    | Attribut | Operator | Värde |
    | --- | --- | --- |
    | sourceObjectType | EQUAL | Användare |
    | cloudMastered | NOTEQUAL | True |

### <a name="step-3-create-the-outbound-sync-rule-required"></a>Steg 3. Skapa den utgående synkroniseringsregel krävs
Den nya regeln för synkronisering måste ha samma **Omfångsfilter** och **högre prioritet** än den befintliga sync-regeln. Detta säkerställer att den nya regeln för synkronisering gäller för samma uppsättning objekt som den befintliga sync-regeln och åsidosätter den befintliga sync-regeln för userCertificate-attributet. Skapa synkroniseringsregel för:
1. I Synchronization Rules Editor klickar du på den **Lägg till ny regel** knappen.
2. Under den **fliken Beskrivning**, ange följande konfiguration:

    | Attribut | Värde | Information |
    | --- | --- | --- |
    | Namn | *Ange ett namn* | T.ex. *”ut till AAD – anpassad åsidosätta för userCertificate”* |
    | Beskrivning | *Ange en beskrivning* | T.ex. *”userCertificate-attributet har mer än 15 värden, exportera NULL”.* |
    | Anslutna System | *Välj den Azure AD-koppling* |
    | Anslutna System objekttyp | **Användaren** | |
    | Typ av Metaversumobjekt | **person** | |
    | Länktyp | **Anslut dig** | |
    | Prioritet | *Om du har valt ett tal mellan 1 och 99* | Det antal valt får inte användas av alla befintliga synkroniseringsregel och har ett lägre värde (och därför högre prioritet) än den befintliga sync-regeln. |

3. Gå till den **Scoping filter** fliken och implementera samma Omfångsfilter som använder den befintliga sync-regeln.
4. Hoppa över den **ansluta regler** fliken.
5. Gå till den **transformationer** flik för att lägga till en ny omvandling med följande konfiguration:

    | Attribut | Värde |
    | --- | --- |
    | Flow-typ |**uttryck** |
    | Målattribut |**userCertificate** |
    | Källattribut |*Använd följande uttryck*: `IIF(IsNullOrEmpty([userCertificate]), NULL, IIF((Count([userCertificate])> 15),AuthoritativeNull,[userCertificate]))` |
    
6. Klicka på den **Lägg till** för att skapa synkroniseringsregel för.

### <a name="step-4-verify-the-new-sync-rule-on-an-existing-object-with-largeobject-error"></a>Steg 4. Kontrollera den nya sync-regeln på ett befintligt objekt med LargeObject-fel
Det här är att verifiera att synkroniseringsregel som skapade fungerar på ett befintligt AD-objekt med LargeObject-fel innan du använder det på andra objekt:
1. Gå till den **Operations** fliken i hanteraren för synkroniseringstjänsten.
2. Välj den senaste exporten till Azure AD-åtgärd och klicka på ett objekt med LargeObject-fel.
3.  I popup-skärmen objektegenskaper för utrymme klickar du på den **förhandsversion** knappen.
4. I popup-skärmen förhandsgranskning väljer **fullständig synkronisering** och klicka på **genomför förhandsversion**.
5. Stäng förhandsgranskningsskärmen och skärmen objektegenskaper för utrymme.
6. Gå till den **kopplingar** fliken i hanteraren för synkroniseringstjänsten.
7. Högerklicka på den **Azure AD** anslutningen och välj **kör...**
8. I popup-fönstret kör anslutningstjänsten, Välj **exportera** steg och klicka på **OK**.
9. Vänta tills exporten till Azure AD för att slutföra och bekräfta att det finns inga fler LargeObject-fel på det markerade objektet.

### <a name="step-5-apply-the-new-sync-rule-to-remaining-objects-with-largeobject-error"></a>Steg 5. Tillämpa den nya regeln för synkronisering av återstående objekt med LargeObject-fel
När synkronisering regeln har lagts till, måste du köra en fullständig synkronisering steget i AD Connector:
1. Gå till den **kopplingar** fliken i hanteraren för synkroniseringstjänsten.
2. Högerklicka på den **AD** anslutningen och välj **kör...**
3. I popup-fönstret kör anslutningstjänsten, Välj **fullständig synkronisering** steg och klicka på **OK**.
4. Vänta tills fullständig synkronisering steg att slutföra.
5. Upprepa stegen ovan för de återstående AD-Anslutningsapparna om du har fler än en AD-Anslutningsappar. Flera anslutningsappar är vanligtvis krävs om du har flera lokala kataloger.

### <a name="step-6-verify-there-are-no-unexpected-changes-waiting-to-be-exported-to-azure-ad"></a>Steg 6. Kontrollera att det finns inga oväntade ändringar som väntar på att exporteras till Azure AD
1. Gå till den **kopplingar** fliken i hanteraren för synkroniseringstjänsten.
2. Högerklicka på den **Azure AD** anslutningen och välj **Search Connector Space**.
3. I popup-fönstret Search Connector Space:
    1. Ställ in omfattning **väntande Export**.
    2. Markera kryssrutorna för alla 3, inklusive **Lägg till**, **ändra** och **ta bort**.
    3. Klicka på **Search** knappen för att returnera alla objekt med ändringar som väntar på att exporteras till Azure AD.
    4. Kontrollera att det finns inga oväntade förändringar. Om du vill kontrollera ändringarna för ett angivet objekt, dubbelklickar du på objektet.

### <a name="step-7-export-the-changes-to-azure-ad"></a>Steg 7. Exportera ändringarna till Azure AD
Så här exporterar ändringarna till Azure AD:
1. Gå till den **kopplingar** fliken i hanteraren för synkroniseringstjänsten.
2. Högerklicka på den **Azure AD** anslutningen och välj **kör...**
4. I popup-fönstret kör anslutningstjänsten, Välj **exportera** steg och klicka på **OK**.
5. Vänta tills exporten till Azure AD för att slutföra och bekräfta det inte finns några fler LargeObject-fel.

### <a name="step-8-re-enable-sync-scheduler"></a>Steg 8. Återaktivera synkroniseringsschemaläggaren
Nu när problemet har lösts återaktivera inbyggda sync scheduler:
1. Starta PowerShell-session.
2. Aktivera schemalagd synkronisering igen genom att köra cmdlet: `Set-ADSyncScheduler -SyncCycleEnabled $true`

> [!Note]
> Föregående steg gäller endast för nyare versioner (1.1.xxx.x) av Azure AD Connect med inbyggda scheduler. Om du använder äldre versioner (1.0.xxx.x) av Azure AD Connect som använder Schemaläggaren i Windows, eller om du använder din egen anpassade scheduler (inte vanliga) för att utlösa Periodisk synkronisering, måste du inaktivera dem enlighet med detta.

## <a name="next-steps"></a>Nästa steg
Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).

