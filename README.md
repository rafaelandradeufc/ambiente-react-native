# Configurando ambiente de desenvolvimento para React-Native no Linux

## Instalando cURL

```
sudo apt-get install curl
```

## Instalando NodeJS

```
curl -sL https://deb.nodesource.com/setup_10.x | sudo bash -

sudo apt install nodejs
```

## Instalando React Native CLI

```
sudo npm install -g react-native-cli
```

## Instalando JDK (Java Development Kit)

Primeiramente execute **java -version** veja se a versão instalada é  **openjdk version "1.8.0_222"**.

Atenção! Se você já estiver com uma versão do JDK instalado diferente da versão **openjdk version "1.8.0_222"** desinstale a sua versão atual com o seguinte comando:

```
sudo apt-get purge --auto-remove openjdk*
```
Agora para instalar a versão correta use:

```
sudo add-apt-repository ppa:openjdk-r/ppa

sudo apt-get update

sudo apt-get install openjdk-8-jdk
```

Executando o **java -version** a versão estará correta com a  **openjdk version "1.8.0_222"**.

## Instalando libs gráficas

```
sudo apt-get install gcc-multilib lib32z1 lib32stdc++6
```

## Configurando SDK do Android no Linux

Crie uma pasta em um local desejado para instalação da SDK. Ex: ~/Android/Sdk

Obs: Anote esse caminho para ser utilizado posteriormente!

Acesse [Android Studio downloads](https://developer.android.com/studio/#downloads) , na opção "Command line tools only" baixe a SDK referente ao seu sistema operacional. Após feito o Download, extraia o conteúdo do pacote para a pasta criada no passo anterior. Com esse endereço precisamos configurar algumas variáveis ambiente em nosso sistema, procure pelo primeiro dos seguintes arquivos existentes no seu sistema: **~/.bash_profile**, **~/.profile**, **~/.zshrc** ou **~/.bashrc**, e adicione essas três linhas no arquivo (de preferência no início):

```
export ANDROID_HOME=~/Android/Sdk
export PATH=$PATH:$ANDROID_HOME/tools
export PATH=$PATH:$ANDROID_HOME/platform-tools
```
Obs: Se nenhum desses arquivos existir, crie o **~/.bash_profile**. Caso esteja utilizando uma pasta diferente para a SDK do Android, altere acima.

Agora, abra seu Terminal e execute o seguinte comando:

```
~/Android/Sdk/tools/bin/sdkmanager "platform-tools" "platforms;android-27" "build-tools;27.0.3"
```

Obs: Aceite todas as licenças digitando **y** caso necessário.


## Criando o primeiro projeto

Para criar o projeto basta abrir o terminal no local onde deseja criar o projeto e executar:

```
react-native init NomeDoProjeto
```

## Iniciando Aplicação


Primeiramente ative a **Depuração USB** no seu celular!
Para ativar a depuração USB no seu dispositivo, primeiro você precisa habilitar o menu "Opções do desenvolvedor" indo em **Configurações → Sobre o telefone** e tocando na linha Build number (Número da versão) na parte inferior sete vezes. Você pode voltar para **Configurações → Opções do desenvolvedor** para ativar a "Depuração USB".


### Executando via cabo USB no celular

Conecte seu celular pelo cabo em seu computador e abra o terminal, verifique o código do fabricante usando o comando:

```
lsusb
```
Instalando o **lsusb** no linux:

```
sudo apt-get install usbutils
```
Ao inserir o comando **lsusb** deve produzir algo como isto:

```
$ lsusb
Bus 002 Device 002: ID 8087:0024 Intel Corp. Integrated Rate Matching Hub
Bus 002 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
Bus 001 Device 003: ID 22b8:2e76 Motorola PCS
Bus 001 Device 002: ID 8087:0024 Intel Corp. Integrated Rate Matching Hub
Bus 001 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
Bus 004 Device 001: ID 1d6b:0003 Linux Foundation 3.0 root hub
Bus 003 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
```
Essas linhas representam os dispositivos USB atualmente conectados à sua máquina.

Você quer a linha que representa o seu telefone. Em caso de dúvida, tente desconectar o telefone e executar o comando novamente:

```
$ lsusb
Bus 002 Device 002: ID 8087:0024 Intel Corp. Integrated Rate Matching Hub
Bus 002 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
Bus 001 Device 002: ID 8087:0024 Intel Corp. Integrated Rate Matching Hub
Bus 001 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
Bus 004 Device 001: ID 1d6b:0003 Linux Foundation 3.0 root hub
Bus 003 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
```
Você verá que, depois de remover o telefone, a linha que possui o modelo do telefone (neste caso, "Motorola PCS") desapareceu da lista. Esta é a linha com a qual nos preocupamos.

```
Bus 001 Device 003: ID 22b8:2e76 Motorola PCS
```
Na linha acima, você deseja obter os quatro primeiros dígitos do ID do dispositivo:

```
22b8:2e76
```

Nesse caso, é **22b8**. Esse é o identificador para a Motorola.

Você precisará inserir isso nas regras no terminal para começar a funcionar:

Obs: Certifique-se de substituir **22b8**  pelo seu identificador no comando abaixo.

```
echo 'SUBSYSTEM=="usb", ATTR{idVendor}=="22b8", MODE="0666", GROUP="plugdev"' | sudo tee /etc/udev/rules.d/51-android-usb.rules
```
Agora verifique se o seu dispositivo está se conectando corretamente ao ADB, o Android Debug Bridge, executando:

```
adb devices
```
Que retornará;

```
List of devices attached
emulator-5554 offline   # Google emulator
14ed2fcc device         # Physical device
```

### Executando via Wi-Fi no celular

Você também pode se conectar ao servidor de desenvolvimento por Wi-Fi. Primeiro, você precisará instalar o aplicativo no seu dispositivo usando um cabo USB, mas depois disso, você poderá depurar sem fio seguindo estas instruções. Você precisará do endereço IP atual da sua máquina de desenvolvimento antes de continuar.

Abra um terminal e digite:
```
ifconfig 
```
Assim você pode obter o endereço IP do seu equipamento.

Agora que você já tem acesso ao IP da sua máquina, abra a aplicação no dispositivo físico (se não estiver aberta), em seguida chacoalhe o dispositivo para abrir o **Developer Menu**, com ele aberto selecione a opção **Dev Settings**, dentro das configurações selecione a opção **Debug server host & port for device**.

No modal que abriu insira o IP da sua máquina, que foi obtido anteriormente, seguido da porta **8081**, por exemplo:

```
10.0.1.1:8081
```
Feito isso você pode voltar para a tela da aplicação, desconectar o cabo que estava conectado na sua máquina, abrir o **Developer Menu** chacoalhando o dispositivo e dar um **Reload**.

Nesse ponto a aplicação deve estar funcionando corretamente e sem o uso de nenhum cabo, utilizando apenas a Rede Wireless.


## Executando a aplicação

Entre na pasta do projeto já criado e execute no terminal os comandos:

### Interpretador de JavaScript
```
npm start
```
ou 
```
react-native start
```
### Executar aplicação

Android:
```
react-native run-android
```
IOS:
```
react-native run-ios
```
