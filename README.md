# Páginas estáticas de Recrutamento

Conjunto de páginas estáticas para alojamento no IIS durante a transição do portal de Recrutamento. Todos os pedidos são reescritos internamente para a página selecionada, sem alterar o URL visível no navegador.

## Páginas disponíveis

| Modo | Ficheiro | Utilização |
| --- | --- | --- |
| `informacao` | `index.html` | Nova área de Recrutamento brevemente disponível. Sem redirecionamento. |
| `redirecionamento` | `index-redirecionamento.html` | Aviso de mudança de endereço e redirecionamento para `https://recrutamento.cm-gaia.pt/`. |
| `manutencao` | `index-manutencao.html` | Aviso de manutenção. Sem redirecionamento. |

## Alterar página ativa

Editar apenas a linha assinalada como `UNICO SWITCH` em `web.config`:

```xml
<rewriteMap name="ModoAtivo" defaultValue="informacao">
  <add key="ativa" value="informacao" />
</rewriteMap>
```

Substituir o valor por uma das opções abaixo e gravar o ficheiro:

```xml
<add key="ativa" value="informacao" />
<add key="ativa" value="redirecionamento" />
<add key="ativa" value="manutencao" />
```

Não é necessário alterar mais nenhuma linha nem reiniciar manualmente o IIS. A configuração é recarregada quando `web.config` é guardado. Os cabeçalhos configurados impedem cache, permitindo que a mudança seja visível de imediato.

## Método de configuração

`web.config` usa dois *rewrite maps* do módulo IIS URL Rewrite:

1. `ModoAtivo` contém o único seletor (`ativa`).
2. `PaginaPorModo` associa cada modo ao respetivo ficheiro HTML.
3. A regra `AvisoParaTudo` reescreve todos os pedidos que não correspondam a ficheiros físicos para o resultado desses mapas.

Exemplo: `ativa = manutencao` resolve para `index-manutencao.html`. O navegador continua a mostrar o endereço originalmente pedido.

## Requisitos e comportamento de reserva

- IIS com o módulo **URL Rewrite** instalado e ativo.
- A pasta deve conter `web.config` e as três páginas HTML.
- Sem o módulo URL Rewrite, o IIS mostra `index.html` como página de reserva para o documento por omissão e erros 404. O seletor único não se aplica nesse cenário.
- Os ficheiros HTML podem ser abertos diretamente para validação visual, mas o acesso normal deve ser feito pela raiz/aplicação IIS.

## Publicação

Copiar a totalidade desta pasta para a localização física configurada no IIS. Manter os quatro ficheiros juntos: `web.config`, `index.html`, `index-redirecionamento.html` e `index-manutencao.html`.
