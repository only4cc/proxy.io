## Perl y Moo y otras hierbas

Quizás en la actualidad *Perl* para algunos no es el lenguaje mas *"sexy"*, sin embargo, hasta el momento he encontrado muy pocos casos que no se puedan resolver utilizándolo y cuando ha sido necesario apoyado en algunos de sus innumerables módulos provistos por la comunidad. Tampoco me ha tocado el caso cuyo rendimiento en tiempo de ejecución fuera bajo como para sugerir alguna complementación, por ejemplo incorporando _"C inline"_.

En el párrafo anterior menciono que en "pocos casos" porque hace un tiempo me tope la necesidad de interactuar con Microsoft Azure en forma programática, pero lamentablemente el sitio no tenia soporte para una API o SDK en Perl y tampoco pude usar con completo éxito algunos módulos experimentales en aquel entonces  alojados en repositorios como el Github (revisando la redacción de este post encontré https://github.com/capside/azure-sdk-perl pero no lo he probado), así que en ese caso termine usando *Python*.

 
Bueno, vamos al grano. Estoy escribiendo una herramienta que permitiría ejecutar consultas interactivas sobre una cuenta de correo IMAP en forma similar a consultar una base de datos en forma simplificada como usando los utilitarios sqlplus de Oracle, mysql de MySQL, etc... es decir un REPL o "Read-Eval-Print-Loop". Empece el desarrollo como un simple script y tempranamente me di cuenta que iva a ser útil enfocarlo con orientación al objeto. En Perl existen varias alternativas de soporte de orientación al objeto  __TMTOWTDI__ :) , pero me pareció que era una buena oportunidad para aprender algunas de las "nuevas" tendencias en este sentido, sobre todo conociendo la aceptación que ha presentado en la comunidad del Camello "Moose" descrito en ingles como el "postmodern object system for Perl 5"  y omnipresente en los mas respetables módulos actuales.

Después de leer los comentarios de algunos Gurus favoritos me decidí por usar **Moo** (*Minimalist Object Orientation*) que es una alternativa mucho mas limitada que Moose pues solo implementa un subconjunto de características pero que por lo que revise contaba con lo suficiente para mis necesidades en este caso. Después de leer la documentación oficial del modulo (en que otro lado que en Metacpan el nuevo sitio que reemplaza el venerable CPAN) la incorpore dentro de mi referido desarrollo. Todo anduvo bien, fácil de entender, de usar, no fue necesario cargar dependencias adicionales y el programa se simplifico y estructuro tal como yo lo esperaba.
 
Hay numerosas y buenas presentaciones y vídeos en relación a Moose y Moo así que dejare que el lector "googlee" a su gusto, pero tal como lo mencionaba para mi fue un grata actualización. 


PD: Revisando me queda pendiente revisar "Mo" un módulo aun más limitado.

## Max Open Files

Aprovecho de compartirles un script en Perl  :)  que genera archivos abiertos simultáneamente, es útil especialmente en Unix/Linux pero también por curiosidad lo ejecuté en un sistema operativo Windows:

```javascript
use Time::HiRes qw( tv_interval );
my $MAX=shift;
system("del c:\\tmp\\maxof\\xxx*");
my $t0 = [gettimeofday];
for ( my $i = 0; $i < $MAX; ++$i ) {
   open ('fh'.$i, '+>/tmp/maxof/xxx'.$i ) or
   die"$!\n$i archivos abiertos antes de morir ...\nduracion: ".(tv_interval ( $t0 )/1000000)." [ms] \n";
}
print "duracion: ".(tv_interval ( $t0 )/1000000)." [ms] \n";
```

Un ejemplo de ejecución en un Windows  8.1:

C:\tmp\maxof>perl maxof.pl  2050
Too many open files
2045 archivos abiertos antes de morir ...
duracion: 1532.11467270232 [ms]

Es decir en 1,5 segundos alcanzo a generar 2045 archivos ...

En un Linux  (Ubuntu 17.10) el máximo de archivos abiertos fue de 1021 (el valor por defecto es un máximo de 1024 descriptores abiertos simultáneamente) prácticamente en el mismo tiempo, pero esta vez ejecutado en un notebook mucho más antiguo:

$ perl maxof.pl 2000
Too many open files
1021 archivos abiertos antes de morir ...
duracion: 1532.30118969526 [ms]
