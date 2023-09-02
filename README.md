# libraryをupdateする際の注意点

## Pillow, PIL

Pillow 10.0.0

- Images.ANTIALIAS was removed in Pillow 10.0.0  
Image.ANTIALIAS -> Image.LANCZOS  
ANTIALIASの代わりにLANCZOSを使用。  
ANTIALIASが追加された当初は、畳み込みに基づく唯一の高品質フィルタだった。ANTIALIASという名前はこれを反映したもの。  
Pillow2.7.0からすべてのリサイズメソッドは畳み込みに基づいている。今後はすべてアンチエイリアス。後方互換性のために、LANCZOSの別名としてANTIALIASは残されていたが削除された。
