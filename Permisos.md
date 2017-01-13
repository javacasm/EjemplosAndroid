# Permisos

## Añadir permisos "a mano" a AndroidManifest.xml

Go to Android Manifest.xml and be sure to add the <uses-permission tag > inside the manifest tag but Outside of all other tags..

<manifest xlmns:android...>

 <uses-permission android:name="android.permission.INTERNET"></uses-permission>
</manifest>

[Ejemplo](http://stackoverflow.com/questions/16948537/adding-permissions-in-androidmanifest-xml-in-android-studio)

![tag for permission](https://i.stack.imgur.com/kQ3v9.png)

![add permission](https://i.stack.imgur.com/VabO6.png)

[Listado de permisos](https://developer.android.com/reference/android/Manifest.permission.html)

A partir de Android 6 no es suficiente con incluir los permisos en el fichero AndroidManifest.xml, si no que además hay que solicitar el permiso al usuario en tiempo de ejecución.

[Un ejemplo de cómo hacerlo](https://developer.android.com/training/permissions/requesting.html)
