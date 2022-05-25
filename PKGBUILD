# Maintainer: rHermes <teodor_spaeren@riseup.net>
# Maintainer: bnavigator <code@bnavigator.de>
# Contributor: PlusMinus
# Contributor: rhabbachi

pkgname=displaylink
pkgver=5.6
_releasedate=2022-05
_pkgfullver=5.6.0-59.176
pkgrel=1
pkgdesc="Linux driver for DL-6xxx, DL-5xxx, DL-41xx and DL-3x00"
arch=('i686' 'x86_64' 'arm' 'armv6h' 'armv7h' 'aarch64')
url="https://www.synaptics.com/products/displaylink-graphics"
license=('custom' 'GPL2' 'LGPL2.1')
depends=('evdi>=1.10.1'
         'libusb>=1.0.0')
makedepends=('grep' 'gawk' 'wget')
install=
changelog="displaylink-release-notes-${pkgver}.txt"
source=(displaylink-driver-${pkgver}.zip::https://www.synaptics.com/sites/default/files/exe_files/${_releasedate}/DisplayLink%20USB%20Graphics%20Software%20for%20Ubuntu${pkgver}-EXE.zip
        displaylink-release-notes-${pkgver}.txt::https://www.synaptics.com/sites/default/files/release_notes/${_releasedate}/DisplayLink%20USB%20Graphics%20Software%20for%20Ubuntu${pkgver}-Release%20Notes.txt
        DISPLAYLINK-EULA
        udev.sh
        99-displaylink.rules 
	displaylink.service 
        displaylink-sleep.sh)
sha256sums=('ca15c82af195f50acc670a6b00b29d6709731e095113619330d45692f5243aed'
            '529d8344f5cc1117388ddf2215c744baa920c7df4dbf40c3e5ddf2b4e82ba8e3'
            '2f81fea43332a62b2cf1dd47e56ea01caf1e886bcd16c3f82b18bfe148fb21a9'
            'dc41ae8a2c287fc50fdda65bad8b0ffd76726f7773c25e1b0c5b7de95cecbdb6'
            'c08a4726cf4e2f92c7cab00168ae9cc8d69d36a67c570609396a4a674934245a'
            '342e83abfe2a38d5635ea928345e933d2ad127ebd3f7caca476663d4f583684b'
            '8be4ab7616e38f91746bdd3e7fafe9004322a8be8e6722389746df9868d576e0')

# Update with > updpkgsums

package() {
  echo "Adding udev rule for DisplayLink DL-3xxx/5xxx devices"
  install -D -m644 99-displaylink.rules "$pkgdir/etc/udev/rules.d/99-displaylink.rules"
  install -D -m755 udev.sh "$pkgdir/opt/displaylink/udev.sh"

  echo "Installing DLM systemd service"
  install -D -m644 displaylink.service "$pkgdir/usr/lib/systemd/system/displaylink.service"
  install -D -m755 displaylink-sleep.sh "$pkgdir/usr/lib/systemd/system-sleep/displaylink.sh"
  
  COREDIR="$pkgdir/usr/lib/displaylink"
  install -d -m755 $COREDIR
  install -d -m755 "$pkgdir/var/log/displaylink"

  echo "Extracting DisplayLink Driver Package"
  pushd $srcdir
  chmod +x displaylink-driver-${_pkgfullver}.run
  ./displaylink-driver-${_pkgfullver}.run \
     --noexec \
     --target $pkgname-$pkgver \
     --nox11 \
     --noprogress
  test -d $pkgname-$pkgver || (echo "Extracting the driver with the .run installer failed"; exit 1)
  pushd $pkgname-$pkgver
  
  case $CARCH in
    i686)
      ARCH="x86-ubuntu-1604" ;;
    x86_64)
      ARCH="x64-ubuntu-1604" ;;
    arm|armv6h|armv7h|aarch64)
      ARCH="arm-linux-gnueabihf" ;;
  esac
  
  echo "Installing DisplayLink Manager $ARCH"
  install -D -m755 $ARCH/DisplayLinkManager $COREDIR/DisplayLinkManager

  echo "Installing firmware packages"
  install -D -m644 *.spkg $COREDIR

  echo "Installing license file"
  install -D -m644 LICENSE "${pkgdir}/usr/share/licenses/${pkgname}/LICENSE"
  popd
  popd
  install -D -m644 DISPLAYLINK-EULA "${pkgdir}/usr/share/licenses/${pkgname}/DISPLAYLINK-EULA"

}
