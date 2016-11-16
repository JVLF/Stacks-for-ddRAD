# Demultiplexing and cleaning 

1. Descomprimir archivo y pasarlo a la carpeta donde se trabajará:
` tar -xvf /path/to/file/lane1.tar -C /path/to/directory/ `
2. Ver uno de los archivos *.fq que se han descargado
`less *.fq.gz`
3. Hacer el archivo que contenga los barcodes, y saber si están ubicados inline o index.
4. Limpiar las lecturas, quitando adaptadores y barcodes, así como eliminar las lecturas con mala calidad
`process_radtags -P -p ./Tutorial/working/clean/lane2/ -b ./Tutorial/working/clean/lane2/lane2_barcodes -o ./Tutorial/working/clean/lane2/ddsamples/ --renz_1 nlaIII --renz_2 mluCI -i gzfastq -c -q -r --inline_index`
[command instructions](http://catchenlab.life.illinois.edu/stacks/comp/process_radtags.php)
5. Concatenar los 4 resultados por individuo
hacer un archivo con los nombres de todos los archivos:
```ls *.gz | sed -E 's/^(sample_)([ACGT\-]+).+/\2/' | uniq -d | cat > indv_samples```
# concatenar los archivos del mismo individuo (que tengan los mismos barcodes):
`$ mkdir samples_concat`
`for i in `cat indv_samples`; do cat sample_$i* > concat_samples_$i.fq.gz; done`
6. Cambiar el nombre de los archivos para que sea algo más significativo (por ejemplo: PP01 o SF22)
```
for ((i=01; i<=48; i++)); do mv concat_samples/*.fq.gz concat_samples/indv_$i.fq.gz; done
ls | cat -n | while read n f; do mv "$f" "indv_$n.fq.gz"; done
for ((i=01; i<=9; i++)); do mv "indv_$i.fq.gz" "indv_0$i.fq.gz"; done
```
