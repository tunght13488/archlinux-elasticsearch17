# $Id$
# Maintainer: Tung Ha <tunght13488@gmail.com>
# Copied from elasticsearch

_pkgname=elasticsearch
pkgname=elasticsearch17
pkgver=1.7.3
pkgrel=1
pkgdesc="Distributed RESTful search engine built on top of Lucene (version 1.x)"
arch=('i686' 'x86_64')
url="https://www.elastic.co/products/elasticsearch"
license=('APACHE')
depends=('java-runtime-headless' 'systemd')
conflicts=('elasticsearch')
install='elasticsearch.install'
source=(
  "http://download.elasticsearch.org/${_pkgname}/${_pkgname}/${_pkgname}-$pkgver.tar.gz"
  elasticsearch.service
  elasticsearch@.service
  elasticsearch-sysctl.conf
  elasticsearch-user.conf
  elasticsearch-tmpfile.conf
  elasticsearch.default
)

sha256sums=('af517611493374cfb2daa8897ae17e63e2efea4d0377d316baa351c1776a2bca'
            'b1a5ecf7e1e4e6125bce2230a5e6cfb34be7126a3c7b1c14a1b23680ba5b22f5'
            'c656f009fff096e73e27a33b736e02c250a31499470d6ff29c70c1c88a19f229'
            'd2130d8587c6a8860d51903b5c2f70703da61cddeb42d526fcda20fd0ec9e99c'
            '794dc12afb1bbecd268ae64079c2590c39b524cf78363f0307956e1089c878be'
            'c9b0044de730769f1e13f22e2ec403e0ab2b63935402fe3d33530ab54d5f908d'
            '97548db0e9c1b34f396dd861f8435cea8b9bb1e355ca93f144a928eaf74d95b3')

backup=('etc/elasticsearch/elasticsearch.yml'
        'etc/elasticsearch/logging.yml'
        'etc/default/elasticsearch')

prepare() {
  cd "$srcdir"/${_pkgname}-$pkgver

  for script in plugin elasticsearch; do
    sed 's|^ES_HOME=.*dirname.*|ES_HOME=/usr/share/elasticsearch|' \
      -i bin/$script
  done

  sed 's|$ES_HOME/lib|/usr/lib/elasticsearch|g' -i bin/elasticsearch.in.sh bin/plugin

  echo -e '\nJAVA_OPTS="$JAVA_OPTS -Des.path.conf=/etc/elasticsearch"' >> bin/elasticsearch.in.sh

  sed -re 's;#\s*(path\.conf:).*$;\1 /etc/elasticsearch;' \
    -e '0,/#\s*(path\.data:).*$/s;;\1 /var/lib/elasticsearch;' \
    -e 's;#\s*(path\.work:).*$;\1 /tmp/elasticsearch;' \
    -e 's;#\s*(path\.logs:).*$;\1 /var/log/elasticsearch;' \
    -i config/elasticsearch.yml
}

package() {
  cd "$srcdir"/${_pkgname}-$pkgver
  install -dm755 "$pkgdir"/etc/elasticsearch

  if [ $CARCH = 'x86_64' ]; then
    install -Dm644 lib/sigar/libsigar-amd64-linux.so "$pkgdir"/usr/lib/elasticsearch/sigar/libsigar-amd64-linux.so
  else
    install -Dm644 lib/sigar/libsigar-x86-linux.so "$pkgdir"/usr/lib/elasticsearch/sigar/libsigar-x86-linux.so
  fi
  cp lib/sigar/sigar*.jar "$pkgdir"/usr/lib/elasticsearch/sigar/
  cp lib/*.jar "$pkgdir"/usr/lib/elasticsearch/

  cp config/* "$pkgdir"/etc/elasticsearch/

  install -Dm755 bin/elasticsearch "$pkgdir"/usr/bin/elasticsearch
  install -Dm755 bin/plugin "$pkgdir"/usr/bin/elasticsearch-plugin
  install -Dm644 bin/elasticsearch.in.sh "$pkgdir"/usr/share/elasticsearch/elasticsearch.in.sh

  install -Dm644 "$srcdir"/elasticsearch.service "$pkgdir"/usr/lib/systemd/system/elasticsearch.service
  install -Dm644 "$srcdir"/elasticsearch@.service "$pkgdir"/usr/lib/systemd/system/elasticsearch@.service
  install -Dm644 "$srcdir"/elasticsearch-user.conf "$pkgdir"/usr/lib/sysusers.d/elasticsearch.conf
  install -Dm644 "$srcdir"/elasticsearch-tmpfile.conf "$pkgdir"/usr/lib/tmpfiles.d/elasticsearch.conf
  install -Dm644 "$srcdir"/elasticsearch-sysctl.conf "$pkgdir"/usr/lib/sysctl.d/elasticsearch.conf

  install -Dm644 "$srcdir"/elasticsearch.default "$pkgdir"/etc/default/elasticsearch

  ln -s ../../../var/lib/elasticsearch "$pkgdir"/usr/share/elasticsearch/data

  chown -R 114:114 "$pkgdir"/usr/share/elasticsearch
}