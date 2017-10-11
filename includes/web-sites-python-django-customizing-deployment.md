Azure kommer att fastställa att ditt program använder Python **om bägge följande villkor stämmer**:

* requirements.txt filen i rotmappen
* någon .py-fil i rotmappen ELLER en runtime.txt som specificerar python

När så är fallet så kommer det att använda ett Python-specifikt distributionsskript vilket utför standardsynkronisering av filer såväl som ytterligare Python-åtgärder som:

* Automatisk hantering av virtuell miljö
* Installation av paket som listas i requirements.txt med pip
* Skapa en lämplig web.config baserat på den valda Python-versionen.
* Samla in statiska filer för Django-program

Du kan kontrollera vissa aspekter av standard-distributionsstegen utan att behöva anpassa skriptet.

Om du vill hoppa över alla Python-specifika distributionssteg så kan du skapa den här tomma filen:

    \.skipPythonDeployment

Om du vill hoppa över insamlingen av statiska filer för Django-programmet:

    \.skipDjango 

För ytterligare kontroll över distributionen så kan du även åsidosätta standard-distributionsskriptet genom att skapa följande filer:

    \.deployment
    \deploy.cmd

Du kan använda den [Azure-kommandoradsgränssnittet] [ Azure command-line interface] att skapa filerna.  Använd det här kommandot från din projektmapp:

    azure site deploymentscript --python

Om de här filerna inte finns så skapar Azure ett tillfälligt distributionsskript och kör det.  Det är identiskt med det som du skapar med kommandot ovan.

[Azure command-line interface]: http://azure.microsoft.com/downloads/
