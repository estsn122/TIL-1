## Capistranoで作られるディレクトリ構成
- releases…releasesディレクトリに、ソースコードが格納される。この時、deployの度にtimestampでディレクトリが作成される。<br>
→リリースごとにソースコードを管理できる
- shared…releasesに作成された各ディレクトリで共通で使用したいファイルなどを格納するためのディレクトリ。

```
[ryota@ip-10-0-11-228 zero_calorie]$ ls
releases  repo  shared

[ryota@ip-10-0-11-228 zero_calorie]$ cd releases/
[ryota@ip-10-0-11-228 releases]$ ls
20200924155049  20200924160755  20200924162817  20200924222823  20200925034307  20200925093711  20200925110025
20200924160307  20200924161532  20200924171329  20200925033913  20200925034959  20200925105812  20200925110220

[ryota@ip-10-0-11-228 releases]$ cd ../repo/
[ryota@ip-10-0-11-228 repo]$ ls
FETCH_HEAD  HEAD  branches  config  description  hooks  info  objects  packed-refs  refs

[ryota@ip-10-0-11-228 repo]$ cd ../shared/
[ryota@ip-10-0-11-228 shared]$ ls
bundle  config  log  public  puma.rb  tmp  vendor
```

