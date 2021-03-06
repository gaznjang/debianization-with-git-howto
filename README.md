# Debianization (with Git) HOWTO.

This file documents how I have created Debian packages for Node, CoffeeScript and Kiwi.

## Prerequisites

I have been building the packages on Ubuntu Karmic.  I needed to install the following packages:

- debhelper
- git-buildpackage
- build-essential
- git2cl (download from http://josefsson.org/git2cl/ and put it somewhere in your PATH)

Export the following variables in your ~/.bashrc

    DEBEMAIL=freshtonic@gmail.com
    DEBFULLNAME="James Sadler"
    export DEBEMAIL DEBFULLNAME

## Building a package

- Make a tarball of the specific upstream revision that you want to build a package from.
  If your upstream is a Git repository, this is easy:

    git archive $TAG | gzip > ../$PROJECT-$TAG-orig.tar.gz

- Create a new Git repository that will be used for maintaining the package.

- Import the upstream source into the package

    git import-orig ../$PROJECT-$TAG-orig.tar.gz

  This creates a new branch called 'upstream' that will contain the version of the upstream code you want to make a package for.
  You should never make your own changes on this branch except for importing a subsequent release of the upstream code.  All of your
  own work will be performed on the master branch.

- Run dh\_make to build the skeleton debian package directory

    dh_make -p $PACKAGE_0.6.1 -f ../$PACKAGE-0.6.1.tar.gz 

- Follow the prompts generated by dh\_make.  If the output involves a compilation step, 'single binary' is 
  probably the type of package you want.  If your package is interpreted code, then 'indep' (architecture independent)
  is most likely the option that you'll need.

- Once that's all done, you'll find yourself on the master branch of your project.  At this point the master branch
  is the same as the upstream branch except it also has a 'debian' directory.  Add and commit this directory now.
  We'll be editing the contents, but if you commit it you'll be able to return to a clean slate without re-running
  dh\_make later if you make a mistake.

- Generate a ChangeLog file.

    git log --pretty --numstat $TAG | git2cl > ChangeLog 

- Generating the package could now in theory be as simple as running 'git buildpackage'.  More than likely it will fail
  and you'll have to edit the debian/rules file.  Even if the package build *does* succeed, there will be a bunch of warnings
  from lintian (like lint but for checking that a Debian package is up to scratch).  The errors may seem hard to understand
  but Google for them and you'll find a good explanation for what they mean.

- Add dependencies to the debian/control file.  Currently you have added no dependencies to your package.  It's a good idea
  that you do, that way when installing your package from apt, it will pull in all of the dependencies.



