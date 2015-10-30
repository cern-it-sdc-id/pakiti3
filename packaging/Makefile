NAME=pakiti
DEB_NAME=pakiti
VERSION=3.0.1
# Leave blank. To be overriden by CI tools.
RELEASE=

CWD=${shell pwd}

PBUILDER_FLAGS=
PBUILDER_TMP="/tmp/"

all: srpm

clean:
	rm -fv *.tar.gz
	rm -fv *.rpm
	rm -fv *.log
	rm -rfv out
	rm -fv *.gz
	rm -fv *.dsc

dist: clean
	tar vczf "$(NAME)-$(VERSION).tar.gz" --exclude="packaging" --exclude-vcs --exclude="*.pyc" --transform="s,^,$(NAME)-$(VERSION)/," ..


override_release: $(SPEC)
	$(if $(RELEASE), sed -i "s/Release:.*/Release: $(RELEASE)%{?dist}/g" "$(SPEC)")

deb-src: dist
	rm -rf "$(PBUILDER_TMP)/$(NAME)-$(VERSION)"
	tar xzf "$(NAME)-$(VERSION).tar.gz" -C "$(PBUILDER_TMP)"
	#mv "$(PBUILDER_TMP)/$(NAME)-$(VERSION)" "$(PBUILDER_TMP)/$(DEB_NAME)-$(VERSION)"
	cp -rv debian "$(PBUILDER_TMP)/$(DEB_NAME)-$(VERSION)"
	$(if $(RELEASE), sed -ri "s/($(DEB_NAME) )\((([0-9]+\.)+[0-9]+)-[0-9]+\)/\\1(\\2-$(RELEASE))/g" "$(PBUILDER_TMP)/$(DEB_NAME)-$(VERSION)/debian/changelog")
	cp -f "$(NAME)-$(VERSION).tar.gz" "$(PBUILDER_TMP)/$(DEB_NAME)_$(VERSION).orig.tar.gz" 
	cd "$(PBUILDER_TMP)/$(DEB_NAME)-$(VERSION)"; \
		debuild -us -uc -S
	cp -f $(PBUILDER_TMP)/$(DEB_NAME)_$(VERSION)*.gz .
	cp -f $(PBUILDER_TMP)/$(DEB_NAME)_$(VERSION)*.dsc .

deb: deb-src
	pbuilder build $(PBUILDER_FLAGS) $(DEB_NAME)_$(VERSION)*.dsc

