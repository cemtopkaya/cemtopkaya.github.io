
Başlatmak:

```sh
bundle exec jekyll serve
```

### Eklenti Kurmak
Örneğin `jekyll-paginate` eklentisini kullanmak isteyelim. `_config.yml` Dosyasında `plugins` dizisine ekleriz:

```yaml
# Build settings
theme: minima
plugins:
  - jekyll-feed
  - jekyll-tagging
  - jekyll-seo-tag
  - jekyll-paginate
```

Eklentinin gem dosyasına `~/Gemfile` dosyasına aşağıdaki satırı ekleriz:

```gemfile
gem 'jekyll-paginate'
```

Eklentinin gem dosyasını yükleriz:

```sh
gem install jekyll-paginate
```

Konsolda eklentinin kurulumu için komutunu çalıştırırız:

```sh
bundle install 
```