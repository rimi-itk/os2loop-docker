# MSO Loop

```sh
docker-compose up --detach
docker-compose run --rm drush --root=/app make --working-copy https://raw.githubusercontent.com/os2loop/profile/master/drupal.make /app/web

cat <<'EOF' > web/sites/default/settings.php
<?php
$databases['default']['default'] = [
  'database' => 'db',
  'username' => 'db',
  'password' => 'db',
  'host' => 'mariadb',
  'port' => '',
  'driver' => 'mysql',
  'prefix' => '',
];

echo http://0.0.0.0:$(docker-compose port nginx 80 | cut -d: -f2)
echo http://os2loop.docker.localhost:$(docker-compose port reverse-proxy 80 | cut -d: -f2)
```

## Running `drush`

```sh
docker-compose run --rm drush --root=/app/web --uri="http://0.0.0.0:$(docker-compose port nginx 80 | cut -d: -f2)"
docker-compose run --rm drush --root=/app/web --uri="http://os2loop.docker.localhost:$(docker-compose port reverse-proxy 80 | cut -d: -f2)" user-login
```




## building the `loopdk` profile

```sh
git clone --branch=master https://github.com/os2loop/profile
docker-compose run --rm drush --root=/app/ make --working-copy --contrib-destination=profiles/loopdk web/profiles/loopdk/drupal.make /app/build
```


## Install `wkhtmltopdf`

```sh
docker-compose exec phpfpm bash -c 'curl --location https://github.com/wkhtmltopdf/wkhtmltopdf/releases/download/0.12.5/wkhtmltox_0.12.5-1.bionic_amd64.deb > /tmp/wkhtmltox.deb'
docker-compose exec phpfpm bash -c 'dpkg --install /tmp/wkhtmltox.deb && apt-get install --yes --fix-broken'
docker-compose run --rm drush --root=/app/web variable-set entity_print_wkhtmltopdf /usr/local/bin/wkhtmltopdf
```


## Add host to hosts

```ssh
docker-compose exec phpfpm bash -c 'echo $(getent hosts host.docker.internal | cut -d" " -f1) os2loop.docker.localhost >> /etc/hosts'
```
