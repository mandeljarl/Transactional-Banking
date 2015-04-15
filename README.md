# Transactional-Banking
Documentación - CIB Transactional Banking

Transactional Banking Documentación
Carpeta con documentación general:
De modo introductorio, esta será la carpeta que contendrá toda la documentación del proyecto:
https://drive.google.com/a/bbvadata.com/?tab=mo#folders/0B2bl2Kvl02TLdVZ2dGg2X3p2cmM
Este documento tendrá como referencia el dashboard realizado para las empresas amigas

Se han seleccionado tres empresas, las cuales pertenecen a los siguientes CNAEs
Tuenti Technologies: 6190. Otras actividades de telecomunicación
AXA: 6511. Seguros de vida
Profesionales Martín Antón: 6832. Gestión y administración de la propiedad inmobiliaria
Carga de tablas:

La primera tabla a cargar es la que contiene a las empresas que nos pasó CIB en verano de 2014. El enlace de la tabla excel es el siguiente: https://drive.google.com/a/bbva.com/file/d/0B2bl2Kvl02TLQ1hXa0ZTaWk1OWM/view?usp=sharing
Para la elaboración de dashboards para “empresas amigas”, se ha incluido a mano Tuenti Technologies puesto que no estaba en un principio pero sí que teníamos información tanto de recibos como de adeudos domiciliados (en cuanto tengamos las tablas de emisores también tendremos adeudos facturados y podremos hacer la división entre recibos domiciliados y facturados)

Se genera la tabla mbor.nifs_seleccionados_rec_sepa_seleccion que contiene las empresas seleccionadas así como las empresas de su sector contenidas en la tabla excel citada anteriormente.

La distribución de empresas por cnae es la siguiente:
cod_cnae empresas
1     6190      200
2     6511      110
3     6832     1609

Tenemos recibos entre las fechas siguientes:
2013-03-31 a 2015-02-28

Tenemos clientes entre las fechas siguientes:
2013-03-31 a 2015-02-28

Las tablas que necesitamos para los recibos son 
da_pro.recibos_local (contiene los datos de los recibos, incluido NIF ordenante y cod_idcontra)
da_pro.recibos_corp (obtenemos el NIF del deudor)


Tenemos clientes entre las fechas siguientes:
2014-02-28 a 2015-02-28 (no hay problema con que empiece más tarde, la última partición contiene todos los clientes)

Las tablas que necesitamos para los datos de los clientes son
da_pro.clientes_corp
da_pro.intervinientes_corp

Generamos mbor.rec que contiene todos los recibos y los nifs de los clientes
Hay un total de 42363327 recibos.

Las tablas que necesitamos para los recibos son:
adeudos_master.domiciliados_local (contiene los datos de los recibos, incluido NIF ordenante y cod_idcontra)
adeudos_master.domiciliados_corp (contiene el NIF del deudor)

Generamos mbor.ade_nif que contiene todos los adeudos y los nifs de los clientes
Hay un total de 87240324 adeudos. (en el cruce de local a corp se pierde menos del 1% de adeudos)
 
Generamos una tabla mbor.rec_ade_nif con la unión de las dos tablas de recibos y adeudos.
Hay un total de 129603651

Divididos por cnae y tipo de recibo/adeudo:

 cod_cnae tipo_rec_ade   EXPR_0
1     6832       adeudo  184827
2     6511       recibo 	27313091
3     6511       adeudo 	42450237
4     6190       recibo 	14867142
5     6190       adeudo 	44605260
6     6832       recibo   	183094




A partir de todos los recibos y adeudos de mbor.rec_ade_nif. Con el nif del deudor vamos a clientes_corp para capturar el cod_client.
(Se podría ir a intervinientes_corp a través del cod_idcontra para conseguir también el cod_client.)
Se construye la tabla mbor.rec_ade_nif_cliente con todos los datos de recibos/adeudos, cod_idcontra, cod_client, nif_ordenante, nif_deudor incluidos.
A continuación, necesitamos los importes de dichos recibos y adeudos y hay que acceder a la tabla:
da_pro.nomina_saldos (contiene el importe de los recibos)

A través de los 5 campos que identifican unívocamente un recibo/adeudo que son:
cod_paisoalf
cod_entalfa
cod_idcontra
cod_detalcto
fec_cierre

Y filtrando mediante cod_secimpot para obtener el importe correcto:
03059 Para recibos
03321 Para adeudos
