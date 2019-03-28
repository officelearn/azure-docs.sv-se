---
title: Azure Active Directory Identity Protection risk-händelser | Microsoft Docs
description: Referera till Azure Active Directory Identity Protection riskhändelser.
services: active-directory
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: mtillman
ms.assetid: 14f7fc83-f4bb-41bf-b6f1-a9bb97717c34
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2018
ms.author: joflore
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: d009022d4ec03990d3ed0321ebcb13c72c674ed8
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2019
ms.locfileid: "58516560"
---
# <a name="azure-active-directory-identity-protection-risk-events-reference"></a>Referera till Azure Active Directory Identity Protection riskhändelser

Merparten av säkerhetsbrott sker när angripare får tillgång till en miljö genom att stjäla en användares identitet. Identifiera komprometterade identiteter är ingen enkelt uppgift. Azure Active Directory använder anpassningsbara maskininlärningsalgoritmer och heuristik för att identifiera misstänkta åtgärder relaterade till dina användarkonton. Varje identifierad misstänkt åtgärd lagras i en post kallas riskhändelse.


## <a name="anonymous-ip-address"></a>Anonym IP-adress

**Identifieringstyp av:** Realtid  
**Gammalt namn:** Inloggningar från anonyma IP-adress


Den här riskhändelsetyp anger inloggningar från en anonym IP-adress (exempel: Tor webbläsare, anonymizer VPN).
Dessa IP-adresser som vanligtvis används av aktörer som vill dölja sina inloggningen telemetri (IP-adress, plats, enhet o.s.v.) för potentiellt skadliga avsikter.


## <a name="atypical-travel"></a>Ovanlig resa

**Identifieringstyp av:** Offline  
**Gammalt namn:** Omöjliga resor till ovanliga platser


Den här riskhändelsetyp identifierar två inloggningar från geografiskt avlägsna platser, där kanske minst en av platserna som också är ovanlig för användaren, får de senaste beteende. Bland flera andra faktorer tar den här maskininlärningsalgoritmen hänsyn till tiden mellan två inloggningar och hur lång tid det skulle ha tagit för användaren att färdas från den första platsen till den andra, som anger att en annan användare använder samma autentiseringsuppgifter.

Algoritmen ignorerar uppenbara ”FALSKT positiva resultat” bidrar till omöjlig resa villkor, till exempel virtuella privata nätverk och platser som ofta används av andra användare i organisationen. Systemet har en inledande inlärningsperiod på det tidigaste på 14 dagar eller 10 inloggningar, då den lär sig en ny användare logga in beteende.


## <a name="leaked-credentials"></a>Läcka ut autentiseringsuppgifter

**Identifieringstyp av:** Offline  
**Gammalt namn:** Användare med läckta autentiseringsuppgifter


Den här riskhändelsetyp anger att användarens giltiga autentiseringsuppgifter har läckts.
När andra nätkriminella kompromettering giltig lösenorden för behöriga användare, dela kriminella ofta dessa autentiseringsuppgifter. Detta görs normalt genom att publicera dem offentligt på mörk webb- eller klistra in platser eller av handel eller sälja autentiseringsuppgifter på svarta marknaden. Microsofts läcka ut autentiseringsuppgifter tjänsten hämtar användarnamn / lösenord kombinationer genom att övervaka offentliga och mörka webbplatser och genom att arbeta med:

- Forskare

- Brottsbekämpande myndigheter

- Security Team på Microsoft

- Andra betrodda källor

När tjänsten skaffar användarens autentiseringsuppgifter från mörka Internet, klistra in platser eller ovan källor kan kontrolleras de mot Azure AD användarnas aktuella giltiga autentiseringsuppgifter att hitta giltig matchningar.


## <a name="malware-linked-ip-address"></a>IP-adress länkad till skadlig kod

**Identifieringstyp av:** Offline  
**Gammalt namn:** Inloggningar från angripna enheter


Den här riskhändelsetyp anger inloggningar från IP-adresser som smittats av skadlig kod som du känner till aktivt kommunicera med en bot-server. Detta fastställs genom att IP-adresserna för användarens enhet mot IP-adresser som fanns i kontakt med en bot-server när bot-servern var aktiv.


## <a name="unfamiliar-sign-in-properties"></a>Obekanta inloggningsegenskaper

**Identifieringstyp av:** Realtid  
**Gammalt namn:** Inloggningar från okända platser

Den här riskhändelsetyp tar hänsyn till tidigare inloggningshistorik (IP, latitud / longitud och ASN) att leta efter avvikande inloggningar. Systemet lagrar information om tidigare platser som används av en användare och så att platserna ”välbekanta”. Riskhändelsen utlöses när inloggningen sker från en plats som inte redan finns i listan med välbekanta platser. Nya användare att i ”inlärningsläget” för en viss tidsperiod i bekant inloggning egenskaperna riskhändelser kommer att inaktiveras när våra algoritmer Läs användarens beteenden. Utbildningsresurser läge varaktighet är dynamisk och beror på hur lång tid det tar att samla in tillräckligt med information om användarens inloggningsmönster algoritmen. Minsta varaktighet är fem dagar. En användare kan gå tillbaka till inlärningsläge efter en lång tids inaktivitet. Systemet ignorerar också inloggningar från välbekanta enheter och platser som ligger geografiskt nära en bekant plats. 

Vi kan också köra den här identifieringen för grundläggande autentisering (eller äldre protokoll). Eftersom dessa protokoll inte har modern egenskaper, till exempel klient-ID, finns det begränsad telemetri för att minska falska positiva identifieringar. Vi rekommenderar våra kunder att flytta till modern autentisering.

