# Regras de atualização

## Ocorrências (`data/ocorrencias.json`)
- Só entram casos de 2024 em diante, verificados na própria página da notícia.
- Campos: `id`, `ano`, `data` (AAAA-MM-DD, data do fato), `local`, `uf`, `regiao`, `polo`, `x`, `y`, `lat`, `lon`, `categoria`, `resumo`, `kg`, `local_aproximado`, `fonte`, `url`.
- `id` novo continua a sequência do ano (`OC-26NN`). Nunca renumerar ids antigos.
- `categoria`: Apreensão de drogas, Pirataria fluvial, Roubo de carga, Invasão de terminal, Desvio de conduta, Operação policial.
- `regiao`: Norte, Nordeste, Centro-Oeste, Sudeste, Sul.
- `kg` só quando a notícia der um número; senão `null`.
- Sem travessões (—) nos textos.
- Polo já existente: reutilizar `x`, `y`, `lat`, `lon` do polo. Polo novo: calcular `x`, `y` assim:

```python
from pyproj import Transformer
tr = Transformer.from_crs('EPSG:4326', '+proj=poly +lat_0=0 +lon_0=-54 +x_0=5000000 +y_0=10000000 +ellps=aust_SA +towgs84=-57,1,-41,0,0,0,0 +units=m +no_defs', always_xy=True)
X, Y = tr.transform(lon, lat)
k = 0.000161701268187 * 15.5 * 0.09216589861751152
x = round((X - 2791531.40873) * k, 1); y = round(-(Y - 10585904.489) * k, 1)
# conferência: Santos (-46.31, -23.96) -> x 690.9, y 752.6
```

## Resumos (`data/resumos.json`)
- Atualização DIÁRIA, logo após a meia-noite (Manaus). A edição cobre a semana de segunda a domingo e é atualizada todo dia.
- Mais nova primeiro em `resumos`. Toda segunda-feira nasce uma edição nova.
- Campos: `edicao` (número sequencial), `id` (data da segunda-feira), `inicio`, `fim`, `publicado_em` (data da última verificação, atualizada todo dia), `titulo`, `destaques` (2 a 4 frases), `noticias` (mais nova primeiro: `data`, `titulo`, `local`, `categoria`, `fonte`, `url`, `ocorrencia_id`), `numeros` (`ocorrencias`, `kg`).
- Texto impessoal, sem primeira pessoa, sem travessões.
- Dia sem novidade: só atualizar `publicado_em` e `atualizado_em`. Edição nova sem notícia ainda: destaques ["Sem ocorrências noticiadas até agora nesta semana."].

Ao final, atualizar `atualizado_em` nos dois arquivos.
