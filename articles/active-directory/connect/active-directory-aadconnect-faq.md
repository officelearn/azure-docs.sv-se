---
title: "Azure Active Directory Connect: Vanliga frågor och svar - | Microsoft Docs"
description: "Den här sidan har vanliga frågor om Azure AD Connect."
services: active-directory
documentationcenter: 
author: billmath
manager: femila
ms.assetid: 4e47a087-ebcd-4b63-9574-0c31907a39a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/12/2017
ms.author: billmath
ms.openlocfilehash: 9d11795aa8b768a8574c3f8bc375b74402825ba3
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/22/2017
---
# <a name="frequently-asked-questions-for-azure-active-directory-connect"></a>Vanliga frågor om Azure Active Directory Connect

## <a name="general-installation"></a>Allmän installation
**F: installationen fungerar om den Azure AD-administratör har aktiverat 2FA?**  
Med versionerna från februari 2016 stöds detta.

**F: finns det ett sätt att installera Azure AD Connect obevakad?**  
Det kan bara användas för att installera Azure AD Connect med installationsguiden. En obevakad och tyst installation stöds inte.

**F: Jag har en skog där en domän inte kan kontaktas. Hur installerar Azure AD Connect?**  
Med versionerna från februari 2016 stöds detta.

**F: hälsoagenten AD DS fungerar på server core?**  
Ja. När du har installerat agenten måste slutföra du registreringen med följande PowerShell-cmdlet: 

`Register-AzureADConnectHealthADDSAgent -Credentials $cred`

## <a name="network"></a>Nätverk
**F: Jag har en brandvägg, nätverksenhet eller något annat som begränsar de maximala tiden anslutningarna kan vara öppen i nätverket. Hur lång tid ska min klient sida timeout tröskelvärdet vara när du använder Azure AD Connect?**  
Alla nätverksprogramvara fysiska enheter eller något annat som begränsar den maximala tiden anslutningar kan förbli öppen bör använda ett tröskelvärde på minst 5 minuter (300 sekunder) för anslutningen mellan den server där Azure AD Connect-klienten är installerad och Azure Active Directory. Detta gäller även alla tidigare Microsoft Identity synkroniseringsverktyg.

**F: är SLDs (enkel etikett domäner) stöds?**  
Nej, Azure AD Connect har inte stöd för lokala skogar och domäner med SLDs.

**F: är skogar med åtskilda AD-domäner som stöds?**  
Nej, Azure AD Connect har inte stöd för lokala skogar som innehåller åtskilda namnområden.

**F: är ”prickad” NetBios-namnet stöds?**  
Nej, Azure AD Connect har inte stöd för lokala skogar och domäner där NetBios-namnet innehåller en punkt ””. i namnet.

**F: är rent IPv6-miljö som stöds?**  
Nej, Azure AD Connect inte stöder endast IPv6-miljö.

## <a name="federation"></a>Federation
**F: Vad gör jag om jag får ett e-postmeddelande som ber mig att förnya certifikatet min Office 365**  
Använd de anvisningar som beskrivs i den [förnya certifikat](active-directory-aadconnect-o365-certs.md) avsnittet om hur du förnyar certifikatet.

**F: Jag har ”Uppdatera automatiskt förlitande part” för O365 förlitande part. Måste jag göra något när min certifikatet för tokensignering automatiskt rullar över?**  
Använd de anvisningar som beskrivs i artikel [förnya certifikat](active-directory-aadconnect-o365-certs.md).

## <a name="environment"></a>Miljö
**F: finns det stöd för att byta namn på servern när du har installerat Azure AD Connect?**  
Nej. Ändrar namnet på servern kan Synkroniseringsmotorn inte kan ansluta till SQL-databasen och tjänsten kommer inte att kunna starta.

## <a name="identity-data"></a>Identitetsdata
**F: UPN (userPrincipalName) attributet i Azure AD matchar inte lokal UPN - varför?**  
Finns i följande artiklar:

* [Användarnamnen i Office 365, Azure eller Intune matchar inte lokal UPN eller alternativ inloggnings-ID](https://support.microsoft.com/en-us/kb/2523192)
* [Ändringarna synkroniserats inte med verktyget Azure Active Directory-synkronisering när du ändrar UPN-namnet för ett användarkonto om du vill använda en annan federerad domän](https://support.microsoft.com/en-us/kb/2669550)

Du kan också konfigurera Azure AD för att tillåta att uppdatera userPrincipalName som beskrivs i Synkroniseringsmotorn [Azure AD Connect sync tjänstens funktioner](active-directory-aadconnectsyncservice-features.md).

**F: är det stöd till mjuka matchning lokala AD-gruppen/kontakt-objekt med den befintliga Azure AD-grupp/kontakta objekt?**  
Nej, detta stöds för närvarande inte.

**F: är det stöd för att manuellt ange ImmutableId-attributet på befintliga Azure AD-grupp/kontakt-objekt som matchar den hårddisk lokala AD-gruppen/kontakt objekt?**  
Nej, detta stöds för närvarande inte.



## <a name="custom-configuration"></a>Anpassad konfiguration
**F: var dokumenteras PowerShell-cmdlets för Azure AD Connect?**  
Andra PowerShell-cmdletar finns i Azure AD Connect stöds inte för kundens användning med undantag för de cmdlets som beskrivs i den här platsen.

**F: kan jag använda ”Server exportservern/importera” hittades i *Synchronization Service Manager* att flytta configuration mellan servrar?**  
Nej. Det här alternativet kommer inte att hämta alla konfigurationsinställningar och ska inte användas. I stället bör du använda guiden för att skapa den grundläggande konfigurationen på den andra servern och använda redigeraren för synkronisering av regeln för att generera PowerShell-skript för att flytta en anpassad regel mellan servrar. Se [svänga migrering](active-directory-aadconnect-upgrade-previous-version.md#swing-migration).

**F: kan cachelagras lösenord för Azure inloggningssidan och kan detta förhindras eftersom den innehåller ett lösenord indataelementet med Komplettera automatiskt = ”false” attributet?**</br>
Vi stöder för närvarande inte ändrar HTML-attribut i ett lösenordsfält, inklusive autocomplete-taggen. Vi arbetar på en funktion som tillåter för anpassad Javascript som gör att du kan lägga till ett attribut i lösenordsfältet. Detta ska vara tillgängliga senare del av 2017.

**F: på Azure-inloggning sidan visas användarnamn för användare som har tidigare har loggat in.  Det här beteendet stängas av?**</br>
Vi stöder för närvarande inte ändra HTML-attribut på sidan logga in. Vi arbetar på en funktion som tillåter för anpassad Javascript som gör att du kan lägga till ett attribut i lösenordsfältet. Detta ska vara tillgängliga senare del av 2017.

**F: finns det ett sätt att förhindra samtidiga sessioner?**</br>
Nej.



## <a name="troubleshooting"></a>Felsökning
**F: hur kan jag få hjälp med Azure AD Connect?**

[Sök i Microsoft Knowledge Base (KB)](https://www.microsoft.com/en-us/Search/result.aspx?q=azure%20active%20directory%20connect&form=mssupport)

* Söka i Microsoft Knowledge Base (KB) för tekniska lösningar på vanliga problem med reparations om stöd för Azure AD Connect.

[Microsoft Azure Active Directory-forum](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)

* Du kan söka och Bläddra för tekniska frågor och svar från gemenskapen eller egna fråga genom att klicka på [här](https://social.msdn.microsoft.com/Forums/azure/en-US/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required).

[Hur du kan få support för Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-troubleshooting-support-howto)

* Använd den här länken för att få support via Azure-portalen.

