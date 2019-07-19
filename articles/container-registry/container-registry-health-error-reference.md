---
title: Fel referens för hälso kontroll-Azure Container Registry
description: Felkoder och möjliga lösningar på problem som hittas genom att köra kommandot AZ ACR check-Health Diagnostic i Azure Container Registry
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 07/02/2019
ms.author: danlep
ms.openlocfilehash: 672d446fa8dc27612c7b046cac109bfa4ca5fec5
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/18/2019
ms.locfileid: "68309381"
---
# <a name="health-check-error-reference"></a>Fel referens för hälso kontroll

Nedan visas information om felkoder som returneras av kommandot [AZ ACR check-Health][az-acr-check-health] . För varje fel visas möjliga lösningar.

## <a name="dockercommanderror"></a>DOCKER_COMMAND_ERROR

Det här felet innebär att Docker-klienten för CLI inte kunde hittas. Därför körs inte följande ytterligare kontroller: hitta Docker-version, utvärdera status för Docker daemon och köra ett Docker pull-kommando.

*Möjliga lösningar*: Installera Docker-klienten; Lägg till Docker-sökvägen till systemvariablerna.

## <a name="dockerdaemonerror"></a>DOCKER_DAEMON_ERROR

Det här felet innebär att status för Docker daemon inte är tillgänglig eller att den inte kan nås med hjälp av CLI. Därför är Docker-åtgärder (till exempel `docker login` och `docker pull`) inte tillgängliga via cli.

*Möjliga lösningar*: Starta om Docker daemon eller kontrol lera att den är korrekt installerad.

## <a name="dockerversionerror"></a>DOCKER_VERSION_ERROR

Det här felet innebär att CLI inte kunde köra kommandot `docker --version`.

*Möjliga lösningar*: Prova att köra kommandot manuellt, kontrol lera att du har den senaste CLI-versionen och undersök fel meddelandet.

## <a name="dockerpullerror"></a>DOCKER_PULL_ERROR

Det här felet innebär att CLI inte kunde hämta en exempel bild till din miljö.

*Möjliga lösningar*: Kontrol lera att alla komponenter som krävs för att hämta en avbildning körs på rätt sätt.

## <a name="helmcommanderror"></a>HELM_COMMAND_ERROR

Det här felet innebär att Helm-klienten inte kunde hittas av CLI, vilket förhindrar andra Helm-åtgärder.

*Möjliga lösningar*: Kontrol lera att Helm-klienten är installerad och att dess sökväg läggs till i systemmiljövariabler.

## <a name="helmversionerror"></a>HELM_VERSION_ERROR

Det här felet innebär att CLI inte kunde fastställa Helm-versionen som är installerad. Detta kan inträffa om Azure CLI-versionen (eller om Helm-versionen) används är föråldrad.

*Möjliga lösningar*: Uppdatera till den senaste versionen av Azure CLI eller till den rekommenderade Helm-versionen; Kör kommandot manuellt och undersök fel meddelandet.

## <a name="connectivitydnserror"></a>CONNECTIVITY_DNS_ERROR

Det här felet innebär att DNS för den registrerade inloggnings servern för registret skickades ping, men inte svarade, vilket innebär att den inte är tillgänglig. Detta kan tyda på problem med anslutningen. Det kan också hända att registret inte finns, användaren kanske inte har behörighet för registret (för att hämta sin inloggnings Server korrekt) eller också är mål registret i ett annat moln än det som används i Azure CLI.

*Möjliga lösningar*: Verifiera anslutning; kontrol lera stavningen av registret och att registret finns. kontrol lera att användaren har rätt behörigheter och att registrets moln är detsamma som används i Azure CLI.

## <a name="connectivityforbiddenerror"></a>CONNECTIVITY_FORBIDDEN_ERROR

Det här felet innebär att utmanings slut punkten för det aktuella registret svarade med en 403-otillåten HTTP-status. Det här felet innebär att användarna inte har åtkomst till registret, förmodligen på grund av en konfiguration av virtuellt nätverk.

*Möjliga lösningar*: Ta bort de virtuella nätverks reglerna eller Lägg till den aktuella klientens IP-adress i listan över tillåtna.

## <a name="connectivitychallengeerror"></a>CONNECTIVITY_CHALLENGE_ERROR

Det här felet innebär att anrops slut punkten för mål registret inte utfärdade någon utmaning.

*Möjliga lösningar*: Försök igen om en stund. Om felet kvarstår öppnar du ett ärende på https://aka.ms/acr/issues.

## <a name="connectivityaadloginerror"></a>CONNECTIVITY_AAD_LOGIN_ERROR

Det här felet innebär att anrops slut punkten för mål registret utfärdade en utmaning, men registret stöder inte Azure Active Directory autentisering.

*Möjliga lösningar*: Försök med ett annat sätt att autentisera, till exempel med administratörs behörighet. Om användarna behöver autentisera med hjälp av Azure Active Directory öppnar du ett problem https://aka.ms/acr/issues på.

## <a name="connectivityrefreshtokenerror"></a>CONNECTIVITY_REFRESH_TOKEN_ERROR

Det här felet innebär att inloggnings servern för registret inte svarade med en uppdateringstoken, så åtkomst till mål registret nekades. Det här felet kan inträffa om användaren inte har rätt behörighet för registret eller om autentiseringsuppgifterna för Azure CLI är inaktuella.

*Möjliga lösningar*: Kontrol lera om användaren har rätt behörigheter för registret. kör `az login` för att uppdatera behörigheter, tokens och autentiseringsuppgifter.

## <a name="connectivityaccesstokenerror"></a>CONNECTIVITY_ACCESS_TOKEN_ERROR

Det här felet innebär att inloggnings servern för registret inte svarade med en åtkomsttoken, så att åtkomsten till mål registret nekades. Det här felet kan inträffa om användaren inte har rätt behörighet för registret eller om autentiseringsuppgifterna för Azure CLI är inaktuella.

*Möjliga lösningar*: Kontrol lera om användaren har rätt behörigheter för registret. kör `az login` för att uppdatera behörigheter, tokens och autentiseringsuppgifter.

## <a name="loginservererror"></a>LOGIN_SERVER_ERROR

Det här felet innebär att CLI inte kunde hitta inloggnings servern för det aktuella registret, och inget standard-suffix hittades för det aktuella molnet. Det här felet kan uppstå om registret inte finns, om användaren inte har rätt behörighet för registret, om registrets moln och det aktuella Azure CLI-molnet inte matchar, eller om Azure CLI-versionen är föråldrad.

*Möjliga lösningar*: Kontrol lera att stavningen är korrekt och att registret finns. kontrol lera att användaren har rätt behörigheter i registret och att molnen i registret och CLI-miljön matchar. Uppdatera Azure CLI till den senaste versionen.

## <a name="next-steps"></a>Nästa steg

Information om alternativ för att kontrol lera hälso tillståndet för ett register finns i [kontrol lera hälso tillståndet för ett Azure Container Registry](container-registry-check-health.md).

Se vanliga frågor och [svar](container-registry-faq.md) om vanliga frågor och andra kända problem med Azure Container Registry.





<!-- LINKS - internal -->
[az-acr-check-health]: /cli/azure/acr#az-acr-check-health
