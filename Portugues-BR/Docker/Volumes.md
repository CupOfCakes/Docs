# Volumes

---

**Volumes** são mecanismos do Docker usados para salvar permanentemente dados gerados ou utilizados por containers, como dados de **bancos de dados**, arquivos **JSON** e planilhas **Excel**.

Os volumes são armazenados no **host**, porém são **gerenciados pelo Docker**, que controla onde esses dados ficam e como são montados nos containers.

Salvar dados em um **volume** separado é mais otimizado do que salvá-los dentro de um **container**, pois, ao escrever no container, o Docker utiliza **camadas adicionais de escrita antes de persistir os dados no disco**. Já os volumes permitem a **escrita direta** no filesystem do host, sem essas camadas extras.

Volumes utilizam **rprivate (recursive private) bind propagation** por padrão, e esse comportamento não é configurável para volumes.

Os volumes existem **independentemente** do ciclo de vida dos containers, persistindo dados mesmo após sua remoção e **podendo ser compartilhados entre múltiplos containers**.

## Volume com arquivos pré existentes

Se um **volume vazio** for montado em um diretório do container que já contém arquivos ou pastas, **esses arquivos serão copiados automaticamente para o volume**.

Se um **volume non empty** for montado em um diretório do container com arquivos ou pastas pré-existentes, **esses arquivos do container serão ocultados pelo volume**. Não há uma forma de remover a montagem para revelar os arquivos ocultos; a maneira correta é **recriar o container sem a montagem**.

Se, ao iniciar um container, for especificado um **volume que ainda não existe**, ele será criado automaticamente como **volume vazio**.

## Volumes anônimos

Um **volume anônimo** é criado quando o desenvolvedor não especifica um nome para o volume.

O Docker atribui a ele um **nome aleatório único** no host. Cada container que utiliza um volume anônimo normalmente recebe **um volume exclusivo**.

Para **compartilhar um volume anônimo** entre containers, é necessário montá-lo usando o **ID do volume** (você pode descobrir o ID com `docker volume ls`).

## Bind Mounts x Volumes

Diferente dos **bind mounts**, os volumes são independentes da estrutura da aplicação e oferecem maior isolamento e portabilidade.

Volumes são mais indicados para **backups e migração de dados**, oferecem maior segurança para compartilhamento, permitem conteúdo **pré-preenchido por containers ou aplicações**, apresentam melhor desempenho de **I/O** e podem ser gerenciados pelo **CLI ou API do Docker**. Além disso, funcionam de forma consistente em ambientes Linux.

Os **bind mounts** são mais indicados quando é necessário acessar diretamente arquivos do host. Portanto, devem ser utilizados em cenários onde há necessidade de compartilhamento direto de arquivos entre o container e o host.

## Comandos Volumes

```cmd
docker volume create #Cria um novo volume
docker volume prune #Remove volumes sem utilização
```

