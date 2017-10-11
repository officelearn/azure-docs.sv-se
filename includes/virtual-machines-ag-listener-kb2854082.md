Sedan om alla servrar i klustret kör Windows Server 2008 R2 eller Windows Server 2012 kan du verifiera att snabbkorrigeringen [KB2854082](http://support.microsoft.com/kb/2854082) är installerad på alla lokala servrar eller virtuella Azure-datorer som ingår i klustret. En server eller virtuell dator som är i klustret, men inte i tillgänglighetsgruppen, bör ha den här snabbkorrigeringen.

Hämtar i fjärrskrivbords-sessionen för var och en av klusternoderna, [KB2854082](http://support.microsoft.com/kb/2854082) till en lokal katalog. Sedan kan installera snabbkorrigeringen på varje klusternod sekventiellt. Om klustertjänsten körs på noden i klustret, startas servern i slutet av installationen av snabbkorrigeringen.

> [!WARNING]
> Stoppa klustertjänsten eller starta om servern påverkar kvorum hälsotillståndet för klustret och tillgänglighetsgruppen och kan leda till att klustret att försättas i offlineläge. För att upprätthålla en hög tillgänglighet på klustret under installationen, se till att:
> 
> * Klustret är i optimala kvorum hälsa. 
> * Innan du installerar snabbkorrigeringen på alla noder är alla noder i klustret online.
> * Innan du installerar snabbkorrigeringen på en annan nod i klustret, kan installationen av snabbkorrigeringen kan köras klart på en nod, inklusive fullständigt omstart av servern.
> 
> 

