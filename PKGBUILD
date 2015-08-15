# Maintainer:  Daniel YC Lin <dlin.tw (at) gmail.com>
pkgname=grub4dos-svn
pkgver=372
pkgrel=2
pkgdesc="A GRUB boot loader support menu on windows(fat,ntfs)/linux(ext2,3,4)"
arch=(i686 x86_64)
url="http://code.google.com/p/grub4dos-chenall"
license=('GPL2')
backup=(etc/grub4dos.conf grub/menu.lst)
makedepends=('svn' 'upx' 'nasm')
install=grub4dos.install
conflicts=(grub4dos)
options=('!strip' '!buildflags' '!makeflags')

# known namcap warnings: these are acceptable
#grub4dos-svn E: ELF file ('usr/sbin/bootlace.com') found in an 'any' package.
#grub4dos-svn W: File (boot) exists in a non-standard directory.

noextract=(unifont.hex.gz)
source=(
  repo::svn+http://grub4dos-chenall.googlecode.com/svn/trunk
  http://grub4dos-chenall.googlecode.com/files/unifont.hex.gz
  README_arch.txt
  grub4dos.conf
  menu.lst
  )
pkgver() {
  cd "$srcdir/repo"
  local ver="$(svnversion)"
  printf "%s" "${ver//[[:alpha:]]}"
}

prepare() {
  cd repo
  # patch for the makefile only allow single thread
  sed -i -e 's:make |:make -j1 |:' build
}
build() {
  cd repo
  #GRUB4DOS_TEMP=$PWD/tmp ./build
  unset CFLAGS
  unset CFLAGS
  unset CXXFLAGS
  unset LDFLGAS
  unset CPPFLAGS
  temp_flags=(`grep -E '^CFLAGS=.+' /etc/makepkg.conf | cut -d '"' -f 2`)
  for i in ${temp_flags[@]};do [ "$i" != "-fstack-protector" ] && CFLAGS+="$i ";done
  CXXFLAGS=("`grep -E '^CXXFLAGS=.+' /etc/makepkg.conf | cut -d '"' -f 2`")
  LDFLAGS=("`grep -E '^LDFLAGS=.+' /etc/makepkg.conf | cut -d '"' -f 2`")
  CPPFLAGS=("`grep -E '^CPPFLAGS=.+' /etc/makepkg.conf | cut -d '"' -f 2`")
  ./configure --enable-preset-menu=preset_menu.lst
  make -j1
  cd stage2
  # generate bootlace64.com
	size=`ls -l bootlace.com|awk '{print $5}'`
	dd if=bootlace.com of=bootlace.head bs=1 count=64
	dd if=bootlace.com of=bootlace.body bs=1 count=`expr $size - 128` skip=64
	dd if=bootlace.com of=bootlace.tail bs=1 count=64 skip=`expr $size - 64`
	cat bootlace.tail bootlace.body bootlace.head > bootlace64.com
	chmod a+x bootlace64.com
	rm -f bootlace.tail bootlace.body bootlace.head
}

package() {
  cd $srcdir
  for _f in menu.lst unifont.hex.gz ; do
    install -Dm 644 "$_f" "$pkgdir/grub/$_f"
  done
  _f=grub4dos.conf ; install -Dm 644 "$_f" "$pkgdir/etc/$_f"
  _f=README_arch.txt ; install -Dm 644 "$_f" "$pkgdir/usr/share/doc/grub4dos/$_f"

  cd $srcdir/repo
  _f=default ; install -Dm 644 "$_f" "$pkgdir/grub/$_f"
  for _f in grub.exe grldr ; do
    install -Dm 644 "stage2/$_f" "$pkgdir/grub/$_f"
  done
  _f=bootlace.com ; install -Dm 755 "stage2/$_f" "$pkgdir/usr/bin/$_f"
  if [ `uname -m` = x86_64 ] ; then
    _f=bootlace64.com ; install -Dm 755 stage2/$_f "$pkgdir/usr/bin/$_f"
  fi

  for _f in README_GRUB4DOS.txt ChangeLog_GRUB4DOS.txt README_GRUB4DOS_CN.txt \
    ChangeLog_chenall.txt ; do

    install -Dm 644 $_f "$pkgdir/usr/share/doc/grub4dos-svn/$_f"
  done
}
md5sums=('SKIP'
         '9afd47aaf5bcc56ab733facdc0ef30db'
         '7e2a1ca368172a216ab5db090aed6df1'
         '254061e71bb66ebeb2cda6103f67b1e7'
         '41e0742abe972a89a5e61f778a8ab8bf')
# vim:set ts=2 sw=2 et sta:
