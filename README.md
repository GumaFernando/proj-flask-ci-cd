# Projeto -  APP Flask - realizando deploy no Azure Web Apps via CI/CD

##  Sobre o Projeto 

Projeto afins de estudos com o objetivo de práticar o uso da ferramenta Azure Pipelines.
Levando em consideração que é um projeto simples de baixa complexidade, NÃO foram realizados os testes unitários na aplicação, pois o objetivo 
é a aplicação E2E do processo de CI/CD.

- Curso realizado de apoio para o desenvolvimento na plataforma UDEMY:

https://www.udemy.com/course/azurepipelines/

### Resumo Aplicação

Criada uma aplicação utilizando a biblioteca Flask do Python, com o objetivo do usuário digitar uma sigla referente
a um FII (fundo imobiliário) e através desse papel digitado o usuário é encaminhado para uma página na qual ele contém as principais informações 
sobre o FII escolhido.

Site na qual o usuário é encaminhado: https://www.fundsexplorer.com.br/funds/

### Objetivo

Após a criação do app flask, foi gerado um DOCKERFILE para execução dos aplicativo em containers (Docker).
Através da ferramenta Azure Pipelines, foi realizado o processo de CI/CD  com o objetivo de realizar uma atualização no código fonte
no file templates/index.html e após essa atualização o processo E2E seria executado desde a criação do build da aplicação até a atualização do serviço 
hospedado no Azure Web Apps de forma automática.


#### Ferramentas utilizadas no desenvolvimento

- Azure Devops Services   
  - Azure Repos 
  - Azure Pipelines 
- Azure container registry
- Azure Web Apps

#### Linguagem do aplicativo

- Python (módulo Flask)

## Códigos

- Código da aplicação utilizando a biblioteca Flask do Python.

```
from flask import Flask, redirect, url_for, render_template,request


app = Flask(__name__)

@app.route("/funds")
@app.route("/", methods=['GET', 'POST'])   # criando rota principal
def bemvindo():
    if request.method == 'POST':
        FII_desejado  = request.form.get('FII_desejado')
        #framework = request.form.get('framework')
        return redirect(f'https://www.fundsexplorer.com.br/funds/{FII_desejado}')
    return render_template('index.html')

       
if __name__  == "__main__":
    app.run(debug= True, host= "0.0.0.0", port=5001) 
```    

- DockerFile para a criação do Build da aplicação
```
FROM  python:3.9.5

RUN apt-get update -y && \
  apt-get install -y python-pip python-dev

COPY . /app

RUN pip install flask

WORKDIR /app

EXPOSE 5001

CMD ["python","app-fii.py"]
```
#
#
#

## Desenvolvimento


- Verificando os agentes utilizados no processo de pipeline CI
  - nesta etapa o DockerFile é selecionado para a criação do build e push dentro do ACR (Azure Container Registry)
  
  ![alt text](https://github.com/GumaFernando/proj-flask-ci-cd/blob/main/ci_cd_evidencias/13_CI_AGENTS.PNG)
  
- Verificando a execução da pipeline, onde foi realizado a primeira etapa MANUALMENTE

  ![alt text](https://github.com/GumaFernando/proj-flask-ci-cd/blob/main/ci_cd_evidencias/1_1_VERIFY_MANUAL_START.PNG)
  
- CI - Verificando o STATUS da execução 

  ![alt text](https://github.com/GumaFernando/proj-flask-ci-cd/blob/main/ci_cd_evidencias/1_CI.PNG)  
  
 - ACR - Verificando a imagem no repositório do ACR (Azure Container Registry)

  ![alt text](https://github.com/GumaFernando/proj-flask-ci-cd/blob/main/ci_cd_evidencias/2_ACR.PNG)  
  
 - CD - Verificando configurações da release

  ![alt text](https://github.com/GumaFernando/proj-flask-ci-cd/blob/main/ci_cd_evidencias/14_CD_CONFIG.PNG)  

 - CD - Verificando execução da release

  ![alt text](https://github.com/GumaFernando/proj-flask-ci-cd/blob/main/ci_cd_evidencias/3_CD.PNG)  
  
- CD - Verificando logs de execução da release, mostrando status de deploy no Azure Web Apps

  ![alt text](https://github.com/GumaFernando/proj-flask-ci-cd/blob/main/ci_cd_evidencias/4_LOG_DEPLOY_WEB_APP.PNG)  
  
- WEB implantação - (Antes da alteração)
  - verificando que o aplicativo está em execução dentro do Azure Web Apps 
  
    ![alt text](https://github.com/GumaFernando/proj-flask-ci-cd/blob/main/ci_cd_evidencias/5_WEB_APP_ANTES.PNG)  
    
 ## Realizando alteração no código fonte e aplicação do CI/CD
 
- Realizando Alteração no código fonte templates/index.html

  ![alt text](https://github.com/GumaFernando/proj-flask-ci-cd/blob/main/ci_cd_evidencias/6_ALTERADO_INDEX.PNG) 
  
- Verificando Trigger CI acionada automaticamente após alteração do código

  ![alt text](https://github.com/GumaFernando/proj-flask-ci-cd/blob/main/ci_cd_evidencias/7_VERIFY_TRIGGER_CI.PNG)
  
- CI - Verificando log da execução da Pipeline 

  ![alt text](https://github.com/GumaFernando/proj-flask-ci-cd/blob/main/ci_cd_evidencias/8_CI_LOG.PNG)  
  
- ACR - Verificando o repositório dentro do ACR com a nova TAG.  :53

  ![alt text](https://github.com/GumaFernando/proj-flask-ci-cd/blob/main/ci_cd_evidencias/9_ACR_NEW_TAG.PNG)
  
- CD - Verificando execução da release para implantar o novo código

  ![alt text](https://github.com/GumaFernando/proj-flask-ci-cd/blob/main/ci_cd_evidencias/1O_CD.PNG)
  
- CD - Verificando o log de execução da release

  ![alt text](https://github.com/GumaFernando/proj-flask-ci-cd/blob/main/ci_cd_evidencias/11_STATUS_CD.PNG)  
  
- Verificando o aplicativo em funcionamento com o NOVO código para o usuário
  - agora com o novo texto: Digite o Fundo Imobiliário desejado !
  
  ![alt text](https://github.com/GumaFernando/proj-flask-ci-cd/blob/main/ci_cd_evidencias/12_WEB_APP_DEPOIS.PNG)  
  
  
  
 ## Considerações Finais
 
Após a realização deste projeto, embora simples com o intuito de botar em prática o conteúdo abordado no curso,
eu pude perceber a importância do CI/CD para um projeto. Se tratando de uma empresa com projetos complexos e com uma equipe
de bastantes desenvolvedores, através dessas práticas combinadas de Integração Contínua e Entrega Contínua podemos perceber o poder dessas práticas
baseado na automação dos processos, facilitando ainda mais no desenvolvimento e implantação  de novas features/bugs no dia-a-dia de uma organização.
Ressaltando que existem diversas abordagens para a realização do Deploy de um app, na qual existem algumas variáveis que devem ser levadas em consideração,
desde a complexidade de execução até as despesas operacionais, devemos ter essa preocupação porém devemos ter em mente o poder de agilidade para entregar os processos e consequentemente gerar valor para o cliente final.
  
 
