# libraryをupdateする際の注意点

## Pillow, PIL

Pillow 10.0.0

- Images.ANTIALIAS was removed in Pillow 10.0.0  
Image.ANTIALIAS -> Image.LANCZOS  
ANTIALIASの代わりにLANCZOSを使用。  
ANTIALIASが追加された当初は、畳み込みに基づく唯一の高品質フィルタだった。ANTIALIASという名前はこれを反映したもの。  
Pillow2.7.0からすべてのリサイズメソッドは畳み込みに基づいている。今後はすべてアンチエイリアス。後方互換性のために、LANCZOSの別名としてANTIALIASは残されていたが削除された。

## PyTorch

- No module named 'torch._six'  
修正前: from torch._six import inf  
修正後: from torch import inf

- torchvisionのpretrainedを使う場合、weights='IMAGENET1K_V1'を使う。  
UserWarning: The parameter 'pretrained' is deprecated since 0.13 and may be removed in the future, please use 'weights' instead.  
UserWarning: Arguments other than a weight enum or `None` for 'weights' are deprecated since 0.13 and may be removed in the future. The current behavior is equivalent to passing `weights=VGG16_Weights.IMAGENET1K_V1`. You can also use `weights=VGG16_Weights.DEFAULT` to get the most up-to-date weights.  
修正前: vgg_model = vgg16(pretrained=True).features[:16]  
修正後: vgg_model = vgg16(weights='IMAGENET1K_V1').features[:16]  

- これから対応  
/torch/functional.py:504: UserWarning: torch.meshgrid: in an upcoming release, it will be required to pass the indexing argument. (Triggered internally at ../aten/src/ATen/native/TensorShape.cpp:3483.)
  return _VF.meshgrid(tensors, **kwargs)  # type: ignore[attr-defined]

```python
def _meshgrid(*tensors, indexing: Optional[str]):
    if has_torch_function(tensors):
        return handle_torch_function(meshgrid, tensors, *tensors, indexing=indexing)
    if len(tensors) == 1 and isinstance(tensors[0], (list, tuple)):
        # the old interface of passing the operands as one list argument
        tensors = tensors[0]  # type: ignore[assignment]

    # Continue allowing call of old method that takes no indexing
    # kwarg for forward compatibility reasons.
    #
    # Remove this two weeks after landing.
    kwargs = {} if indexing is None else {'indexing': indexing}
    return _VF.meshgrid(tensors, **kwargs)  # type: ignore[attr-defined]
```
