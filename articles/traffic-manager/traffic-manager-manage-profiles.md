<properties
   pageTitle="Hantera Azure Traffic Manager-profiler | Microsoft Azure"
   description="Den här artikeln beskriver hur du skapar, inaktiverar, aktiverar, tar bort och visar historiken för en Azure Traffic Manager-profil."
   services="traffic-manager"
   documentationCenter=""
   authors="sdwheeler"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/17/2016"
   ms.author="sewhee" />


# Hantera en Azure Traffic Manager-profil

Du använder en Traffic Manager-profil för att ange vilka slutpunkter för molntjänster eller webbplatser som ska övervakas av Traffic Manager och vilken trafikroutningsmetod som du vill använda för att distribuera anslutningar till dessa slutpunkter.

## Skapa en Traffic Manager-profil med hjälp av Snabbregistrering

Du kan snabbt skapa en Traffic Manager-profil genom att använda Snabbregistrering på den klassiska Azure-portalen. Med Snabbregistrering kan du skapa profiler med grundläggande konfigurationsinställningar. Men du kan inte använda Snabbregistrering för konfigurationen av slutpunkter (molntjänster och webbplatser), redundansordningen för trafikroutningsmetoden för redundans eller övervakningsinställningar. När du har skapat din profil kan du konfigurera dessa inställningar på den klassiska Azure-portalen. Traffic Manager har stöd för upp till 200 slutpunkter per profil. De flesta användningsscenarier kräver dock endast ett litet antal slutpunkter. 

### Skapa en ny Traffic Manager-profil

1. **Distribuera dina molntjänster och webbplatser i produktionsmiljön.** Mer information om molntjänster finns i [Cloud Services](http://go.microsoft.com/fwlink/p/?LinkId=314074). Information om molntjänster finns i [Bästa praxis](https://msdn.microsoft.com/library/azure/5229dd1c-5a91-4869-8522-bed8597d9cf5#bkmk_TrafficManagerBestPracticesProfile). Mer information om webbplatser finns i [Websites](http://go.microsoft.com/fwlink/p/?LinkId=393327).

2. **Logga in på den klassiska Azure-portalen.** Skapa en ny Traffic Manager-profil genom att klicka på **Ny** längst ned till vänster på portalen, klicka på **Network Services > Traffic Manager** och klicka sedan på **Snabbregistrering** för att börja konfigurera din profil.
3. **Konfigurera DNS-prefixet.** Ge din Traffic Manager-profil ett unikt DNS-prefixnamn. Du kan ange bara prefixet för ett Traffic Manager-domännamn.
4. **Välj prenumerationen.** Välj lämplig Azure-prenumeration. Varje profil är associerad med en enda prenumeration. Om du bara har en prenumeration visas inte det här alternativet.
5. **Välj trafikroutningsmetoden.** Välj trafikroutningsmetod i **trafikroutningsprincip**. Mer information om trafikroutningsmetoder finns i [Om Traffic Manager-trafikroutningsmetoder](traffic-manager-routing-methods.md).
6. **Klicka på Skapa för att skapa den nya profilen**. När profilkonfigurationen har slutförts hittar du din profil i Traffic Manager-fönstret på den klassiska Azure-portalen.
7. **Konfigurera slutpunkter, övervakning och ytterligare inställningar på den klassiska Azure-portalen.** Eftersom du bara kan konfigurera grundläggande inställningar med hjälp av Snabbregistrering måste du konfigurera fler inställningar, till exempel listan över slutpunkter och redundansordningen för slutpunkter, för att slutföra konfigurationen. 


## Inaktivera, aktivera eller ta bort en profil

Du kan inaktivera en befintlig Traffic Manager-profil så att den inte refererar användarförfrågningar till dess konfigurerade slutpunkter. När du inaktiverar en Traffic Manager-profil förblir själva profilen och den information som den innehåller intakta och kan redigeras i Traffic Manager-gränssnittet. Om du vill återaktivera profilen kan du enkelt göra det på den klassiska Azure-portalen så återupptas hänvisningarna. När du skapar en Traffic Manager-profil på den klassiska Azure-portalen aktiveras den automatiskt. Om en profil inte längre behövs kan du ta bort den.

### Så här inaktiverar du en profil

1. Ändra DNS-resursposten för din Internet-DNS-server så att den använder lämplig posttyp och pekare till antingen ett annat namn eller IP-adressen för en specifik plats på Internet. Du ändrar alltså DNS-resursposten på Internet-DNS-servern så att den inte längre använder en CNAME-resurs som pekar på domännamnet för Traffic Manager-profilen.
2. Trafiken slutar att dirigeras till slutpunkterna via Traffic Manager-profilinställningarna.
3. Välj den profil som du vill inaktivera. När du väljer en profil markerar du den på Traffic Manager-sidan genom att klicka på kolumnen bredvid profilnamnet. Klicka inte på namnet på profilen eller på pilen bredvid namnet eftersom det gör att inställningssidan för profilen öppnas.
4. När du har valt profilen klickar du på **Inaktivera** längst ned på sidan.

### Så här aktiverar du en profil

1. Välj den profil som du vill aktivera. När du väljer en profil markerar du den på Traffic Manager-sidan genom att klicka på kolumnen bredvid profilnamnet. Klicka inte på namnet på profilen eller på pilen bredvid namnet eftersom det gör att inställningssidan för profilen öppnas.
2. När du har valt profilen klickar du på **Aktivera** längst ned på sidan.
3. Ändra DNS-resursposten på Internet-DNS-servern så att den använder CNAME-posttypen, som mappar företagets domännamn till domännamnet för din Traffic Manager-profil. Mer information finns i [Peka ett företags Internet-domän på en Traffic Manager-domän](traffic-manager-point-internet-domain.md).
4. Trafiken börjar dirigeras till slutpunkterna igen.

### Så här tar du bort en profil

1. Kontrollera att DNS-resursposten på Internet-DNS-servern inte längre använder en CNAME-resurspost som pekar på domännamnet för Traffic Manager-profilen.
2. Välj den profil som du vill ta bort. När du väljer en profil markerar du den på Traffic Manager-sidan genom att klicka på kolumnen bredvid profilen. Klicka inte på namnet på profilen eller på pilen bredvid namnet eftersom det gör att inställningssidan för profilen öppnas.
4. När du har valt profilen klickar du på **Ta bort** längst ned på sidan.

## Visa Traffic Manager-profilens ändringshistorik

Du kan visa ändringshistoriken för din Traffic Manager-profil på den klassiska Azure-portalen i Hanteringstjänster.

### Så här visar du ändringshistoriken för Traffic Manager

1. Klicka på **Hanteringstjänster** i den vänstra rutan på den klassiska Azure-portalen.
2. Klicka på **Åtgärdsloggar** på sidan Hanteringstjänster.
3. På sidan Åtgärdsloggar kan du filtrera fram ändringshistoriken för Traffic Manager-profilen. När du har valt filtreringsalternativ klickar du på bockmarkeringen för att visa resultatet.
   - Om du vill visa profiländringar för alla dina profiler väljer du din prenumeration och ett tidsintervall och väljer sedan **Traffic Manager** från snabbmenyn **Typ**.
   - Om du vill filtrera efter profilnamn skriver du namnet på profilen i fältet **Tjänstnamn** eller väljer den från snabbmenyn.
   - Om du vill visa information för varje enskild ändring markerar du raden med den ändring som du vill visa och klickar sedan på **Information** längst ned på sidan. I fönstret **Åtgärdsinformation** kan du visa en XML-representation av API-objektet som skapades eller uppdaterades som en del av åtgärden och kopiera XML-koden till Urklipp.


## Nästa steg

[Lägga till en slutpunkt](traffic-manager-endpoints.md)

[Konfigurera routningsmetoden för redundans](traffic-manager-configure-failover-routing-method.md)

[Konfigurera routningsmetoden för resursallokering](traffic-manager-configure-round-robin-routing-method.md)

[Konfigurera routningsmetoden för prestanda](traffic-manager-configure-performance-routing-method.md)

[Felsöka degraderat Traffic Manager-tillstånd](traffic-manager-troubleshooting-degraded.md)


<!--HONumber=Sep16_HO3-->


