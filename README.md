# climaduino
Criação de um Sistema de Estações Meteorológicas para Monitoramento do Clima

1. Introdução

Reativação do projeto Monitora Cerrado apresentado na Semana Nacional de Ciência e Tecnologia em 2011.
2. Hardware

Primeira versão do projeto utilizava Arduino e Attiny85;

Testando novas possibilidades utilizando o ESP 8266. Algumas oficinas foram feitas no Calango com o mestre Jerônimo.

Branati também fez oficina de IoT - Internet of Things
2.1 Sensores

A estação básica funciona com um sensor de temperatura e umidade, mas outros sensores podem também ser acrescentados a qualquer momento. Os sensores podem ser fabricados industrialmente ou, conforme o caso, construídos pelos próprios interessados.

    Temperatura e umidade: DHT11 (menos preciso), DHT 22(mais usado nas estações atualmente em operação), Si7021, HTU21d, BMe280…
    Pressão atmosférica: BMP085 (mais comum)
    Sensor de luminosidade (baseado em LDR)
    Sensor de velocidade do vento (anemômetro)
    Sensor de direção do vento
    Sensor básico de chuva (indica se está chovendo ou não)
    Pluviometro (indica a quantidade de chuva) (do tipo “tipping bucket” Depois…)
    Sensor de profundidade para reservatórios
    Outras Sugestões???
    * Modulo BMe280, pressão, temperatura e umidade integrados em um único módulo….

Código base para utilização no ESP 8266 01 e afins com NodeMCU:

https://gist.github.com/chaeplin/ee0fef774d2e79e0c236

3. MQTT

Provisoriamente rodando no endereço climaduino.ddns.net porta padrão 1883, utilizando o broker mosquitto.
3.1 Tópicos

Exemplos sendo utilizados:

    Sítio Toca da Coruja - Fabio - Lago Oeste: /climaduino/tocadacoruja
    Casa - Luiz Fellipe - Park Way: /climaduino/parkway
    Casa - Paulo - Altiplano Leste: /climaduino/altiplano
    Apartamento - Renato - Aguas Claras: /climaduino/branatihouse
    Casa - Felipe - Vila Planalto: /climaduino/vilaplanalto

4. Node-RED

Provisoriamente rodando no endereço http://climaduino.ddns.net:1880

Exemplo de painel com dados recebidos: http://climaduino.ddns.net:1880/ui/#/0

5. Docker

Estamos utilizando as imagens:

    mysql/mysql-server
    ansi/mosquitto
    nodered/node-red-docker

Os comandos abaixo instalarão as imagens, respectivamente, e iniciarão a execução :

docker run -p 3306:3306 –name climaduino_mysql -v /home/docker_mysql:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=[COLOQUE AQUI SEU PASSWORD DO BANCO MYSQL] -d mysql/mysql-server

docker run -p 1883:1883 –name mosquitto -d ansi/mosquitto

docker run -it -p 1880:1880 –name mynodered nodered/node-red-docker
6. Proposta de padrão para mensagens MQTT do projeto Climaduino

O tópico principal é climaduino, o subtópico é o nome da estação

Exemplo:

/climaduino/nomeestat (substituir nomeestat pelo nome de cada estação. evitar de usar na estação o nome de uma localidade, pois poderão haver outras estações na mesma localidade )

A mensagem (Payload) deverá usar palavras controladas para definir os campos de dados e metadados mais importantes, facilitando sua organização futura em um Banco de dados:

Exemplo de mensagem mqtt padrão para as estações do Monitora Cerrado:

{ “Loc”: “LLLLL”, “EstTp”: “EE”, “UF”: “UU”, “Lat”:00.00, “Long”:00.00, “AltM”: 0000.0, “Sens”: “SSSSSS”, “TempC”:00.00, “Humd”: 00.00, “Rain”: “R”, “precip1h”:000, “precip24h”:000, “Windspms”: 000.00 }

OBS: Apenas campos com dado devem ser enviados.

Abaixo, descrição e comentários sobre os campos…

“Loc”: “LLLLL”, > String com a localidade da estação. (ex: Setor sudoeste)

“EstTp”: “EE”, > String com o tipo da estação: RI = rural indoor/interna , RO = rural outdoor/externa, UI = urbana indoor, UO = Urbana Outdoor

“UF”: “UU”, > String com a Unidade da federação da estação. (ex: DF)

“Lat”:00.00, > Latitude da estação em graus decimais

“Long”:00.00, > Longitude da estação, em graus decimais

“AltM”: 0000.00, > Altitude da estação, em metros

“Sens”: “SSSSSS”, > String com informação dos sensores utilizados (ex: DHT22, BMP280…)

“TempC”:00.00, > Temperatura em graus centigrados

“Humd”: 000.00, > Umidade em %

“Rain”: “R”, > intensidade da chuva, com três estados: S = Strong/Forte,W= Weak/Fraca, N= No rain/Sem chuva (informação de sensores baratos de chuva)

“precip1h”:000, > precipitação na última hora, em mm

“precip24h”:000, > precipitação nas últimas 24 horas, em mm

“Windspms”: 000.00 > Velocidade do vento, em m/s
7. Outras referências técnicas

Outros projetos de estações meteorológicas

https://electrosome.com/iot-data-logger-arduino-esp8266/

http://randomnerdtutorials.com/esp8266-wireless-weather-station-with-data-logging-to-excel/

http://www.instructables.com/id/ESP8266-Wifi-Temperature-Logger/

https://www.youtube.com/watch?v=gAtVs-FuY-M

https://www.youtube.com/watch?v=cQ-8zNzfm7o

https://tttapa.github.io/ESP8266/Chap16%20-%20Data%20Logging.html

https://github.com/esp8266/Arduino/blob/master/libraries/SD/examples/Datalogger/Datalogger.ino

https://www.hackster.io/detox/transmit-esp8266-data-to-google-sheets-8fc617

https://elementztechblog.wordpress.com/2015/05/13/esp8266-based-temperature-data-logger-using-arduino/

https://hackaday.io/project/12599-esp8266-web-serverdata-logger

Critérios para escolha do local de instalação da estação meteorológica:

https://www.wunderground.com/weatherstation/installationguide.asp

Importância de utilização de um abrigo adequado para as estações e algumas sugestões de construção:

https://en.wikipedia.org/wiki/Stevenson_screen

https://es.wikipedia.org/wiki/Abrigo_meteorol%C3%B3gico

http://meteoropole.com.br/2014/08/apresentando-o-abrigo-meteorologico/

https://www.monolitonimbus.com.br/estacao-e-instrumentos-meteorologicos/

http://www.ufjf.br/labcaa/equipamentos/

http://www.instructables.com/id/Stevenson-Screen-weather-station/

http://revistas.fca.unesp.br/index.php/energia/article/viewFile/1665/pdf_27

https://www.researchgate.net/publication/282023209_Miniabrigos_meteorologicos_comparacao_e_analise_estatistica_para_avaliacao_de_eficiencia

https://www.researchgate.net/publication/308674489_Aquisicao_de_dados_meteorologicos_atraves_da_plataforma_Arduino_construcao_de_baixo_custo_e_analise_de_dados

Otimizando o consumo de energia do Arduino e ESP8266 para estações funcionando a bateria ou sistemas fotovoltaicos:

https://openhomeautomation.net/arduino-battery/

https://www.open-electronics.org/the-power-of-arduino-this-unknown/

http://www.homautomation.org/2014/04/03/best-ways-to-power-a-arduino-according-to-your-need/

http://www.instructables.com/id/Arduino-Battery/

https://forum.arduino.cc/index.php?topic=463291.0

http://www.esp8266.com/viewtopic.php?f=13&t=3875

http://forum.arduino.cc/index.php?topic=266452.0

Sensores faça-vc-mesmo

http://forum.arduino.cc/index.php?topic=337474.0

https://edwardmallon.wordpress.com/2015/07/08/measuring-humidity-in-a-cave-a-masons-hyrgometer-experiment/

http://hvac-talk.com/vbb/showthread.php?1537521-Wet-Bulb-Using-a-Sock

http://fairtradetobacco.com/threads/2970-How-to-Make-A-Wet-Bulb-Dry-Bulb-Thermometer-Set-Up-on-the-CHEAP

https://www.backyardchickens.com/threads/how-to-make-a-wet-bulb-thermometer.546891/

Acuracia de sensores de umidade

http://www.kandrsmith.org/RJS/Misc/Hygrometers/calib_dht22.html

http://www.kandrsmith.org/RJS/Misc/Hygrometers/calib_dht22_dht11_sht71.html

http://www.kandrsmith.org/RJS/Misc/Hygrometers/calib_many.html

http://hackaday.com/2017/01/03/humidity-sensor-shootout/

http://akizukidenshi.com/download/ds/aosong/AM2302.pdf

https://learn.adafruit.com/dht/overview
