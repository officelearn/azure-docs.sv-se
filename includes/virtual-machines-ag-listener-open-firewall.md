I det här steget kan du skapa en brandväggsregel för att öppna avsökningsport för slutpunkten för Utjämning av nätverksbelastning (59999 som angavs tidigare) och en annan regel för att öppna porten för tillgänglighetsgruppens lyssnare. Eftersom du har skapat belastningsutjämnade-slutpunkten på virtuella datorer som innehåller tillgänglighetsgrupprepliker måste du öppna porten avsökning och lyssningsport på respektive virtuella datorer.

1. Starta på virtuella datorer som är värdar för repliker, **Windows-brandväggen med avancerad säkerhet**.

2. Högerklicka på **regler för inkommande trafik**, och klicka sedan på **ny regel**.

3. På den **regeltyp** väljer **Port**, och klicka sedan på **nästa**.

4. På den **protokoll och portar** väljer **TCP**, typen **59999** i den **specifika lokala portar** rutan och klicka på  **Nästa**.

5. På den **åtgärd** behåller **tillåter anslutningen** markerad och klicka sedan på **nästa**.

6. På den **profil** , accepterar du standardinställningarna och klicka sedan på **nästa**.

7. På den **namn** sidan den **namn** text anger du ett namn för regeln som **alltid på avsökning lyssningsport**, och klicka sedan på **Slutför**.

8. Upprepa föregående steg för porten för tillgänglighetsgruppens lyssnare (som anges tidigare i parametern $EndpointPort av skriptet) och anger ett lämpligt namn, till exempel **alltid på lyssningsport**.

