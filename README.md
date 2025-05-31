# dio-entrega-continua-com-devops-services-e-data-factory
Repo do Azure DevOps Services com Data Factory para Versionamento e Backups

### Os conceitos e resumo foram retirados da leitura da documentação oficial <https://learn.microsoft.com/pt-br/azure/data-factory/source-control>

#### A integração com repositórios Git (Azure Repos ou GitHub) com Data Factory apresenta uma excelente solução de versionamento e backups com os seguintes pontos:
- Rastreamento e Auditoria de Alterações: A integração com o Git permite que todas as modificações nos recursos da sua Data Factory (pipelines, datasets, serviços vinculados, etc.) sejam versionadas. Isso significa que é possível rastrear quem fez o quê, quando e por que, oferecendo um histórico completo e auditável de todas as alterações.
- Capacidade de Reverter Alterações: Uma das maiores vantagens do controle de versão é a habilidade de reverter para versões anteriores do código. Se uma alteração introduzir um bug ou um problema, é fácil voltar para uma versão funcional anterior, minimizando o tempo de inatividade e os impactos negativos.
- Alterações Incrementais e Estado: A solução garante que as alterações incrementais dos recursos da Data Factory sejam registradas, independentemente do estado atual. Isso significa que você tem um registro detalhado de cada pequena modificação, o que é crucial para depuração e compreensão da evolução dos seus pipelines.
- Colaboração e Controle: O Git facilita a colaboração entre os membros da equipe através de processos de revisão de código. Isso melhora a qualidade do código, garante a adesão a padrões e permite um controle mais rigoroso sobre as mudanças que são introduzidas no ambiente.
- Base para CI/CD e Backups: Embora não explicitamente chamado de "backup" no sentido tradicional de dados, o repositório Git atua como um backup de todo o código e configuração da sua Data Factory. Se a instância da Data Factory for perdida ou corrompida, ela pode ser recriada a partir do código versionado no Git. Além disso, essa integração é fundamental para a implementação de pipelines de Integração Contínua e Implantação Contínua (CI/CD), permitindo a automação da implantação das suas fábricas de dados.

- Para criar um repositório Git no Azure DevOps Services Repos com Azure CLI se usa a extensão azure-devops da Azure CLI para gerenciar recursos do Azure DevOps. Instale-a com o comando `az extension add --name azure-devops`.
- Autenticação no Azure DevOps: Você precisará de um Personal Access Token (PAT) do Azure DevOps com as permissões apropriadas (pelo menos Code (Read & Write)) para criar repositórios:
```
# Primeiro login com sua conta Azure
az login

# Depois, configure a organização e o projeto padrão do Azure DevOps
az devops configure --defaults organization=https://dev.azure.com/<sua-organizacao> project=<seu-projeto-devops>

# Para login com PAT (se necessário, ou se já estiver logado com az login e tiver PAT configurado no Azure DevOps)
# az devops login --organization https://dev.azure.com/<sua-organizacao>
# Será solicitado seu PAT
```
- Comando para criar um novo repositório Git: `az repos create --name <nome-do-repositorio>`.
- Integrar o repositório Git do Azure DevOps com o Azure Data Factory via Azure CLI: embora a interface gráfica do Data Factory Studio seja o método mais comum para configurar a integração Git, a Azure CLI (e a API REST subjacente) oferece um comando específico para configurar ou atualizar as informações do repositório de uma fábrica de dados: az datafactory configure-factory-repo. Pré-requisitos:
  * Data Factory existente, deve-se ter uma instância do Azure Data Factory.
  * Repositório Git no Azure DevOps existente: O repositório que você criou na etapa anterior.
  * Extensão Azure Data Factory para CLI: `az extension add --name datafactory`
- Comando para configurar a integração Git:
```
az datafactory configure-factory-repo --resource-group <nome-do-resource-group> \
    --factory-name <nome-da-data-factory> \
    --factory-repo-configuration "{ \
        \"type\": \"FactoryVSTSGitRepo\", \
        \"accountName\": \"<nome-da-organizacao-devops>\", \
        \"projectName\": \"<nome-do-projeto-devops>\", \
        \"repositoryName\": \"<nome-do-repositorio-git>\", \
        \"collaborationBranch\": \"main\", \
        \"rootFolder\": \"/\" \
    }"
```
- Considerações para Versionamento de Código e Backups:
  * Versionamento: Uma vez configurada a integração, todas as alterações (pipelines, datasets, linked services, etc.) feitas no Azure Data Factory Studio serão salvas na sua branch de colaboração no repositório Git. Isso fornece controle de versão completo.
  * Backups: O repositório Git atua como um backup de todos os seus artefatos do Data Factory. Em caso de perda de dados no ADF ou necessidade de restaurar uma versão anterior, você pode reconstruir a fábrica de dados a partir do código no Git.
  * CI/CD: Essa integração é a base para a implementação de processos de Integração Contínua (CI) e Implantação Contínua (CD), permitindo automatizar a implantação de seus pipelines do ADF em diferentes ambientes (desenvolvimento, teste, produção).


