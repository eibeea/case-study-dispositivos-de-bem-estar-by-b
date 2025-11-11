# case-study-dispositivos-de-bem-estar-by-b
<div align="center">
<img src="https://img.freepik.com/vetores-gratis/conceito-de-saude-de-auto-cuidado_23-2148517202.jpg">
</div>


# Introdução
> Esta análise é sobre o mercado de dispositivos de inteligência na área de saúde feminina, para a empresa Bellabeat, com foco especial no crescimento da empresa através de análise de dados para novas tendências. Bellabeat é uma pequena empresa fabricante de produtos de alta tecnologia voltados à saúde da mulher e, por ter potencial para adquirir maior participação no mercado global de dispositivos inteligentes, a CEO da Bellabeat acredita que encontrarão novas oportunidades de crescimento através da análise de dados de condicionamento físico a partir de dispositivos inteligentes. Os conjuntos de dados utilizados nesta análise foram gerados pelos participantes de uma pesquisa distribuída entre 12.03.2016 e 12.05.2016. Foram observados os dados de trinta usuários qualificados do Fitbit que consentiram o envio de dados pessoais como: o resultado em nível de minuto para atividade física, frequência cardíaca e monitoramento do sono. Esperamos que esta análise esclareça qualquer dúvida quanto às etapas de análise necessária para o aprimoramento da empresa em questão.

# Análise dos Dados
**1. Tarefa de Negócios**
Identificar tendências na utilização de dispositivos inteligentes Fitbit para obter informações sobre como os consumidores estão usando estes dispositivos, retirar insights importantes que ajudarão a orientar a estratégia de marketing da empresa e aplicar em um produto da BellaBeat.


Partes interessadas:
Urška Sršen: Cofundadora e CEO da Bellabeat
Sando Mur: Matemático e cofundador da Bellabeat;
Equipe de análise de marketing da Bellabeat


**2. Descrição da fonte de dados usada**
Foi usado o Dataset FitBit Fitness Tracker, disponibilizado por Mobius. São dados do rastreamento de condicionamento físico dos envios consentidos pelos usuários sobre a utilização do dispositivo Fitbit. O conjunto de dados analisado são dados abertos, ou seja, estão disponíveis para o público, mas não contém imparcialidade e não é atual, pois foram fornecidos dados pessoais de apenas 33 pessoas no período de 12.03.2016 e 12.05.2016, pela Amazon Mechanical Turk.


**3. Processar e limpar dados**
Os conjuntos de dados foram analisados no programa RStudio, aproveitando da alta funcionalidade da linguagem R. Foram aplicadas importação dos arquivos, remoção de duplicatas, certificação de dados únicos e consistentes, formatação numérica quando necessário, realizada a média de comparação e gráficos de comparação.


Documentação de qualquer limpeza ou manipulação de dados:


### Instalação
```r
install.packages("tidyverse")
install.packages("dplyr")
install.packages("janitor")
install.packages("ggplot2")
library(tidyverse)
library(dplyr)
library(janitor)
library(ggplot2)
```

### Import FitBit
```r
# importando planilhas
df_dailyactivity <- read.csv("/kaggle/input/fitbit/Fitabase Data 4.12.16-5.12.16/dailyActivity_merged.csv")
df_sleepday<- read.csv("/kaggle/input/fitbit/Fitabase Data 4.12.16-5.12.16/sleepDay_merged.csv")
df_calorie <- read.csv("/kaggle/input/fitbit/Fitabase Data 4.12.16-5.12.16/minuteCaloriesNarrow_merged.csv")
```

### Limpeza
```r
#Visualizacao das 6 primeiras linhas
head(df_dailyactivity)
head(df_sleepday)
head(df_calorie)
```

```r
# Id distinto
n_distinct(df_dailyactivity$Id)
n_distinct(df_sleepday$Id)
n_distinct(df_calorie$Id)
```


> [!NOTE] 
> A partir dessa análise, foi constatado que há 33 participantes distintos, mas foi contabilizado apenas 24 dados de sono.

```r
dim(df_dailyactivity)
dim(df_sleepday)
dim(df_calorie)
```

```
# Exibe as informaçoes do arquivo
str(df_dailyactivity)
```

```r
# Certifica que os nomes das colunas são únicos e consistentes
df_dailyactivity <- df_dailyactivity %>%
  drop_na() %>%
  distinct() %>%
  clean_names()

df_sleepday <- df_sleepday %>%
  drop_na() %>%
  distinct() %>%
  clean_names()

df_calorie <- df_calorie %>%
  drop_na() %>%
  distinct() %>%
  clean_names()

```
  
```r
# Média de passos por participante
mean_step_daily <- df_dailyactivity %>%
  group_by(id) %>%
  summarize(mean_steps = mean(total_steps)) 
head(mean_step_daily)
```

```r
# Média total de passos
mean_step <- df_dailyactivity %>%
  summarize(mean_steps = mean(total_steps)) 
mean_step
```


>[!NOTE]
> De acordo com a OMS, 10 mil passos por dia é a quantidade de exercício necessária para se manter saudável. A maioria dos participantes caminham em média 7 mil passos apenas.

```r
# Média de tempo na cama
mean_sleep <- df_sleepday %>% 
  group_by(id) %>%
  summarize(mean_sleep = mean(total_time_in_bed))
head(mean_sleep)
```


```r
# Média total na cama
mean_s <- df_sleepday %>% 
  summarize(mean_s = mean(total_time_in_bed))
mean_s
```


>[!NOTE]
>Observamos que apenas 24 dos participantes contabilizaram as horas na cama, e a média dos dados é de 7h.

```r
# Média das calorias por participantes
mean_calories <- df_calorie %>%
  group_by(id) %>%
  summarize(mean = mean(calories))
head(mean_calories)
```


```r
# Média total
mean_c <- df_calorie %>%
  summarize(mean_c = mean(calories))
mean_c
```


>[!NOTE]
> Observamos os dados dos participantes e obtivemos uma média de 1600 calorias por dia.






## Gráficos de comparação
```r
ggplot(data=df_dailyactivity, aes(x=total_steps, y=calories)) +  
  stat_smooth(method = "lm", color="red", fill="white") + 
  geom_point(alpha=0.4, position = position_jitter(), color="purple") +
  labs(title = "Passos diários vs. Calorias", subtitle = "Comparação de caminhada e calorias queimadas", caption = "(based on data from FitBit)") +
  theme(plot.title = element_text(size=20, color="#993399"), plot.subtitle = element_text(size=10, color = "#ac58aa"), plot.caption = element_text(color="#993399"))
```
<div align="center">
<img src="https://www.kaggleusercontent.com/kf/144352448/eyJhbGciOiJkaXIiLCJlbmMiOiJBMTI4Q0JDLUhTMjU2In0..JbA91qwBnysnxhkMjP78Dg.tkt12a8q7WxGWx81sy5O9662kdmK6SrfRDSD7wUoD840vkvgpvFKmLjBvdbhEA4Kz2y1OUIzKFx-OWvXd6XQRvsn5ipX6Kl5ZaMKGfpA9HFqWiFSlPxcpnQxeRY0SnNctiNiBsxyCphZhWZGJRiHsfg9gUXXY5runJ3Ex3ddxAfLahthqw5u7xBTRXfnH3rSkMo8XL71cNp_KdxVGHyR171KwA85KBkA8MqZrDKqeZPJUr2l1sBivWqn-SsexYpPG5de3zDPX4PT8UIoWNJxj_WOu0SevbEIK-w7Z4JdeJ_THc0NhFnSNr3LpR6d8slZ2XtaBS3HIHyyXQ4mtEwN0hBwJ386yabd8toe1GapYHK8beVP7LKSdMI7TayO2AnlZfooG7T00jMV3FsWN8UEA5qnRhZoCGTS-X--D4EtKbiaM7zqfoQSANhB2WNOvm--X-_rnnCovk9E4l6US8ym61RxKrtZsEQdzhpcfaqUTlAopNnKNR-q5_Hzz8X6QfsN3z--yZQmKiQxqHyO-wHuhYSU9lyMOWnf_0N9uAag1B7gImTugUbwjLJ30GV4nwvTb6C11QH_oT-z-pTHFkA9CMxwMxnTSV4Np96I3GdAoIPXx2s52oWedFHpvUfmfb_UwqgBRp2z03Ej9reabffUUT9lPyTM3mfiJKjC0A_FtRE.bR3Kwl2OwjCBSuu_l64QPA/__results___files/__results___21_1.png" width=400px >
</div>


>[!NOTE]
> Há correlação de passos dados em um dia e as calorias que foram queimadas.

```r
ggplot(data = mean_calories, aes(x=mean, y=id)) + stat_smooth(method = "lm", color="red", fill="white") +``
  geom_point(alpha=0.4, position = position_jitter(), color="purple") +
  labs(title = "Calorias", subtitle = "Média das calorias queimadas por participante", caption = "(based on data from FitBit)") +
  theme(plot.title = element_text(size=20, color="#993399"), plot.subtitle = element_text(size=10, color = "#ac58aa"), plot.caption = element_text(color="#993399"))
```

<div align="center">
<img src="https://www.kaggleusercontent.com/kf/144352448/eyJhbGciOiJkaXIiLCJlbmMiOiJBMTI4Q0JDLUhTMjU2In0..ZbsqXvFB49AhwBxvPsREAQ.lZXGBX8ozvwaAFjE4gilA9dO-qgS8hIFGkkr3aLJdUvd5R5kHxuJHrzKt9xK96fwtwQLFt_kWFzjTfuUCyHIR196JvJ5P9zwxrE_bRGeZw99WT3Yyv4iwrDqR6vDMWBBknG1-ef6bh1YGEEfLRMGRHfUFLyX0kgV2r61R4NO_X9UJpmSVuMKAbtGW5niRd1ct28TwFMDfLOvEte1lf_FoodF1TzVwpNxEOEE1EiK8S5cSF_jZKNbfLWZkpuC8YKAzYoBN9Kh6GaLKIXBqklaUZ3j-fld8BI63XHJCuQV5GblHfRND4QvMNfLy3kjL2h8H5IAWYiQSAbxpUIBlNiWtuacOLq671rbPe8ERo2YxgA3pHdu3XSdrUOHBLBVrFm3VXqd05eHAGirsDCnfwVW9RGZzQZDfgA7LJoamtzsoFnRzZJlxwiimRKNQm4IxPbeQXLN8E4Ags1LXzWsGInuaNLZruzOx1yZrlo0_5THk_M28tQ52nvnqXJHSrfGn-zfBHELtIKhqkZ6qM8bkjV1M-MakWpFi3-LN1T8zkPvRM6Zwl_DecGUH-GiqbXYxXWPDlc63_XXPVb1R3sESyMxutJRl7Sa4U9YhDOqCZKLn6tvXhraoIVco6zL6-JAeteg4KcoMpQdokF0lxLlZATFCVTk0Bct9hyc65cE_4YRCp4.pqQZle7ipC_zXAJmRG_7Dg/__results___files/__results___23_1.png" width=400px >
</div>

>[!NOTE]
>A média das calorias queimadas por participante mostra a diversidade de cada um.

```r
ggplot(data = mean_sleep, aes(x=mean_sleep, y=id)) + stat_smooth(method = "lm", color="red", fill="white") +
  geom_point(alpha=0.4, position = position_jitter(), color="purple") +
  labs(title = "Tempo de sono", subtitle = "Média do tempo de sono por participante", caption = "(based on data from FitBit)") +
  theme(plot.title = element_text(size=20, color="#993399"), plot.subtitle = element_text(size=10, color = "#ac58aa"), plot.caption = element_text(color="#993399"))
```
<div align="center">
<img src="https://www.kaggleusercontent.com/kf/144352448/eyJhbGciOiJkaXIiLCJlbmMiOiJBMTI4Q0JDLUhTMjU2In0..ZbsqXvFB49AhwBxvPsREAQ.lZXGBX8ozvwaAFjE4gilA9dO-qgS8hIFGkkr3aLJdUvd5R5kHxuJHrzKt9xK96fwtwQLFt_kWFzjTfuUCyHIR196JvJ5P9zwxrE_bRGeZw99WT3Yyv4iwrDqR6vDMWBBknG1-ef6bh1YGEEfLRMGRHfUFLyX0kgV2r61R4NO_X9UJpmSVuMKAbtGW5niRd1ct28TwFMDfLOvEte1lf_FoodF1TzVwpNxEOEE1EiK8S5cSF_jZKNbfLWZkpuC8YKAzYoBN9Kh6GaLKIXBqklaUZ3j-fld8BI63XHJCuQV5GblHfRND4QvMNfLy3kjL2h8H5IAWYiQSAbxpUIBlNiWtuacOLq671rbPe8ERo2YxgA3pHdu3XSdrUOHBLBVrFm3VXqd05eHAGirsDCnfwVW9RGZzQZDfgA7LJoamtzsoFnRzZJlxwiimRKNQm4IxPbeQXLN8E4Ags1LXzWsGInuaNLZruzOx1yZrlo0_5THk_M28tQ52nvnqXJHSrfGn-zfBHELtIKhqkZ6qM8bkjV1M-MakWpFi3-LN1T8zkPvRM6Zwl_DecGUH-GiqbXYxXWPDlc63_XXPVb1R3sESyMxutJRl7Sa4U9YhDOqCZKLn6tvXhraoIVco6zL6-JAeteg4KcoMpQdokF0lxLlZATFCVTk0Bct9hyc65cE_4YRCp4.pqQZle7ipC_zXAJmRG_7Dg/__results___files/__results___25_1.png" width=400px >
</div>


```r
ggplot(data=df_sleepday, aes(x=total_minutes_asleep, y=total_time_in_bed)) + geom_point(alpha=0.4, position = position_jitter(), color="purple") + stat_smooth(method = "lm", color="red", fill="white") +
  labs(title = "Minutos acordado vs. Minutos na cama", subtitle = "Comparação dos minutos", caption = "(based on data from FitBit)") +
  theme(plot.title = element_text(size=20, color="#993399"), plot.subtitle = element_text(size=10, color = "#ac58aa"), plot.caption = element_text(color="#993399"))
```

<div align="center">
<img src="https://www.kaggleusercontent.com/kf/144352448/eyJhbGciOiJkaXIiLCJlbmMiOiJBMTI4Q0JDLUhTMjU2In0..ZbsqXvFB49AhwBxvPsREAQ.lZXGBX8ozvwaAFjE4gilA9dO-qgS8hIFGkkr3aLJdUvd5R5kHxuJHrzKt9xK96fwtwQLFt_kWFzjTfuUCyHIR196JvJ5P9zwxrE_bRGeZw99WT3Yyv4iwrDqR6vDMWBBknG1-ef6bh1YGEEfLRMGRHfUFLyX0kgV2r61R4NO_X9UJpmSVuMKAbtGW5niRd1ct28TwFMDfLOvEte1lf_FoodF1TzVwpNxEOEE1EiK8S5cSF_jZKNbfLWZkpuC8YKAzYoBN9Kh6GaLKIXBqklaUZ3j-fld8BI63XHJCuQV5GblHfRND4QvMNfLy3kjL2h8H5IAWYiQSAbxpUIBlNiWtuacOLq671rbPe8ERo2YxgA3pHdu3XSdrUOHBLBVrFm3VXqd05eHAGirsDCnfwVW9RGZzQZDfgA7LJoamtzsoFnRzZJlxwiimRKNQm4IxPbeQXLN8E4Ags1LXzWsGInuaNLZruzOx1yZrlo0_5THk_M28tQ52nvnqXJHSrfGn-zfBHELtIKhqkZ6qM8bkjV1M-MakWpFi3-LN1T8zkPvRM6Zwl_DecGUH-GiqbXYxXWPDlc63_XXPVb1R3sESyMxutJRl7Sa4U9YhDOqCZKLn6tvXhraoIVco6zL6-JAeteg4KcoMpQdokF0lxLlZATFCVTk0Bct9hyc65cE_4YRCp4.pqQZle7ipC_zXAJmRG_7Dg/__results___files/__results___26_1.png" width=400px >
</div>

```r
ggplot(data=df_dailyactivity, aes(x=sedentary_minutes, y=very_active_minutes)) + 
  geom_point(alpha=0.4, position = position_jitter(), color="purple") + 
  stat_smooth(method = "lm", color="red", fill="white") +
  labs(title = "Minutos parados vs. Minutos ativos", subtitle = "Comparação dos minutos", caption = "(based on data from FitBit)") +
  theme(plot.title = element_text(size=20, color="#993399"), plot.subtitle = element_text(size=10, color = "#ac58aa"), plot.caption = element_text(color="#993399"))
```
<div align="center">
<img src="https://www.kaggleusercontent.com/kf/144352448/eyJhbGciOiJkaXIiLCJlbmMiOiJBMTI4Q0JDLUhTMjU2In0..ZbsqXvFB49AhwBxvPsREAQ.lZXGBX8ozvwaAFjE4gilA9dO-qgS8hIFGkkr3aLJdUvd5R5kHxuJHrzKt9xK96fwtwQLFt_kWFzjTfuUCyHIR196JvJ5P9zwxrE_bRGeZw99WT3Yyv4iwrDqR6vDMWBBknG1-ef6bh1YGEEfLRMGRHfUFLyX0kgV2r61R4NO_X9UJpmSVuMKAbtGW5niRd1ct28TwFMDfLOvEte1lf_FoodF1TzVwpNxEOEE1EiK8S5cSF_jZKNbfLWZkpuC8YKAzYoBN9Kh6GaLKIXBqklaUZ3j-fld8BI63XHJCuQV5GblHfRND4QvMNfLy3kjL2h8H5IAWYiQSAbxpUIBlNiWtuacOLq671rbPe8ERo2YxgA3pHdu3XSdrUOHBLBVrFm3VXqd05eHAGirsDCnfwVW9RGZzQZDfgA7LJoamtzsoFnRzZJlxwiimRKNQm4IxPbeQXLN8E4Ags1LXzWsGInuaNLZruzOx1yZrlo0_5THk_M28tQ52nvnqXJHSrfGn-zfBHELtIKhqkZ6qM8bkjV1M-MakWpFi3-LN1T8zkPvRM6Zwl_DecGUH-GiqbXYxXWPDlc63_XXPVb1R3sESyMxutJRl7Sa4U9YhDOqCZKLn6tvXhraoIVco6zL6-JAeteg4KcoMpQdokF0lxLlZATFCVTk0Bct9hyc65cE_4YRCp4.pqQZle7ipC_zXAJmRG_7Dg/__results___files/__results___27_1.png" width=400px >
</div>

>[!NOTE]
> Nota-se uma ligeira inclinação, abordando a provável falta de utilização do dispositivo em alguns momentos do dia.



# Finalização
> Como mencionado anteriormente, a Bellabeat, uma empresa de alta tecnologia que fabrica produtos inteligentes fundada em 2013, cresceu e continua crescendo na indústria trazendo preocupação constante na área da saúde e bem-estar feminino. E esse apreço permitiu que mais mulheres se conhecessem e procurassem melhorar sua própria saúde e hábitos.
Após analisar os dados do Fibit encontramos insights que viriam a nos ajudar na criação de novas tendências de negócios.
O público alvo da empresa seria as mulheres, as que trabalham, as que ficam em casa e as que trabalham em casa. Estas, que desejam e necessitam realizar alguma atividade, leve ou moderada, para que se mantenham saudáveis e tenham um dia melhor, precisam de algum tipo de medidor para constatarem estar no caminho certo.

> Os dispositivos inteligentes já ajudam de alguma forma, mas podemos melhorar, por exemplo:
na área de recomendações de atividades caso o participante deseje perder peso,
sugestões de alimentação,
documentação dos registros de horários que equilibrem com a rotina do participante,
notificações para lembrar horários como hora de dormir, hora de beber água,
indicações de maneiras de dormir melhor,
lembrar de sempre utilizar os dispositivos para que as notificações estejam de acordo com a rotina,
uma pequena competição para usar de incentivo a sempre estar buscando resultados melhores, notificações para exibir as mudanças ao longo do tempo.

> A partir disso, aprimorar os dispositivos para que sejam seguros, leves, duradouros, elegantes, resistentes e criar campanhas sobre a importância de manter a saúde em dia.

> Por fim, me despeço. Este é meu primeiro projeto de estudo de caso em R analisando dados sobre dispositivos inteligentes para obter insights que ajudarão na estratégia de marketing da empresa Bellabeat. Sigo em busca de novos conhecimentos para essa jornada na área de dados.
Obrigada por continuarem comigo até aqui!






# License
This Notebook has been released under the Apache 2.0 open source license.
