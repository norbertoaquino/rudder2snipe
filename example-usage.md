# Exemplo de Uso do rudder2snipe

## Configuração Inicial

1. **Configure suas credenciais no arquivo settings.conf:**

```bash
cp settings-rudder.conf.example settings.conf
```

2. **Edite o arquivo settings.conf com suas informações:**

```ini
[rudder]
url = https://seu-servidor-rudder.dominio.com
api_token = SEU-TOKEN-API-RUDDER-AQUI

[snipe-it]
url = https://sua-instancia-snipe.dominio.com
apikey = SUA-CHAVE-API-SNIPE-AQUI
manufacturer_id = 2
defaultStatus = 2
computer_model_category_id = 2

[api-mapping]
name = hostname
_snipeit_mac_address_1 = properties ipHostNumber
_snipeit_os_2 = properties osName
_snipeit_os_version_3 = properties osVersion
```

## Comandos Básicos

### Teste de Conexão
```bash
python3 rudder2snipe --connection_test
```

### Execução em Modo Dry Run (Teste sem alterações)
```bash
python3 rudder2snipe --dryrun --verbose
```

### Execução Normal
```bash
python3 rudder2snipe --verbose
```

### Execução com Rate Limiting (recomendado para instâncias grandes)
```bash
python3 rudder2snipe --verbose --ratelimited
```

### Forçar Atualização de Todos os Assets
```bash
python3 rudder2snipe --verbose --force
```

## Mapeamento de Campos Rudder → Snipe-IT

O rudder2snipe mapeia automaticamente os seguintes campos do Rudder para o Snipe-IT:

| Campo Rudder | Campo Snipe-IT | Descrição |
|--------------|----------------|-----------|
| `hostname` | `name` | Nome do asset |
| `properties.serialNumber` | `serial` | Número de série |
| `properties.machineType` | `model` | Tipo/modelo da máquina |
| `properties.ipHostNumber` | `_snipeit_mac_address_1` | Endereço IP/MAC |
| `properties.osName` | `_snipeit_os_2` | Sistema operacional |
| `properties.osVersion` | `_snipeit_os_version_3` | Versão do SO |
| `properties.manufacturer` | `_snipeit_manufacturer_5` | Fabricante |
| `properties.memorySize` | `_snipeit_memory_6` | Quantidade de memória |

## Logs e Debugging

### Para logs detalhados:
```bash
python3 rudder2snipe --debug
```

### Para informações básicas:
```bash
python3 rudder2snipe --verbose
```

## Solução de Problemas

### Erro de Autenticação Rudder
- Verifique se o token API do Rudder está correto
- Confirme se o token tem permissões de leitura para nós

### Erro de SSL
```bash
python3 rudder2snipe --do_not_verify_ssl
```

### Rate Limiting
```bash
python3 rudder2snipe --ratelimited
```

## Exemplos de Configurações Avançadas

### Mapeamento Personalizado de Usuários
```ini
[user-mapping]
rudder_api_field = properties username
```

### Asset Tag Personalizado
```ini
[snipe-it]
asset_tag = properties serialNumber
```

### Campo Personalizado no Snipe-IT
Para mapear um campo personalizado, primeiro obtenha o nome do campo DB no Snipe-IT e adicione ao mapeamento:

```ini
[api-mapping]
_snipeit_custom_field_123 = properties customProperty
```
