# BotAutoLogin

BotAutoLogin é um projeto que automatiza o processo de login em aplicações web. Ele foi desenvolvido como exemplo de uso das funcionalidades de CI/CD para integração com o Orquestrador BotCity.

## Pipelines

- [Linter](#linter)
- [Deploy e Release](#orquestrador)


### Linter
A integração com o linters garante que o código siga as melhores práticas e padrões de estilo.

Esse fluxo pode ser executado de diversas formas, no arquivo `.github/workflows/ruff.yml`, o gatilho do fluxo está definido da seguinte maneira:

```yaml
on:
  push:
    branches:
      - dev
```

Nesse caso deve-se utilizar a branch `dev` para realizar as modificações necessárias no projeto e ao enviá-las para o GitHub com o comando `git push`, a Action será disparada e fará a verificação do código.

### Orquestrador
A integração com o Orquestrador BotCity vai agilizar o processo de `deploy` e `release` de uma versão do código através do arquivo `.github/workflows/deploy.yml`, com o seguinte gatilho:

```yaml
on:
  push:
    tags:
      - '*'
```

Esse gatilho faz com que a Action seja disparada sempre que uma nova [tag](https://docs.github.com/pt/enterprise-server@3.17/desktop/managing-commits/managing-tags-in-github-desktop) for criada no repositório.

O valor da tag criada será recuperada, dinamicamente, através da variável de ambiente `GITHUB_REF`:

```yaml
- name: Definir versão da tag
    run: echo "TAG_VERSION=${GITHUB_REF#refs/tags/}" >> $GITHUB_ENV
```

Outro ponto importante, para a conexão segura com o Orquestrador BotCity, é necessário o uso de [credenciais de desenvolvimento](https://documentation.botcity.dev/pt/maestro/features/dev-environment/). Você pode armazenar essas credenciais como [segredos no GitHub](https://docs.github.com/pt/enterprise-server@3.17/actions/how-tos/write-workflows/choose-what-workflows-do/use-secrets) e acessá-las da seguinte forma:

```yaml
- name: Deploy Bot
env:
    SERVER: ${{ secrets.SERVER }}
    LOGIN: ${{ secrets.LOGIN }}
    KEY: ${{ secrets.KEY }}
run: |
    cicd-pipeline-utils/scripts/bot.sh deploy -version "${{ env.TAG_VERSION }}" -botFile "./BotAutoLogin.zip" -botId "BotAutoLogin" -type "python" -repository "dev"
```

## Conclusão

Desta forma você pode automatizar o processo de `deploy` e `release` do seu bot, garantindo que as melhores práticas sejam seguidas e que as credenciais sensíveis sejam mantidas em segurança.

---

> Veja exemplos de outras [plataformas de pipeline](https://github.com/botcity-dev/cicd-pipeline-utils/tree/main/examples).

> Para mais informações, consulte a [documentação oficial do BotCity](https://documentation.botcity.dev/pt/cicd-integration/).