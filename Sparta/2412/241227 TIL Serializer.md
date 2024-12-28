#TIL #스파르타코딩클럽 [[2412]]

## 1. Serializer
### Article에 Comment 추가하기
- Nested Relationships
	- Serializer는 기존 필드를 override하거나 추가적인 필드를 구성 가능
	- 이때 모델 사이에 참조 관계가 있다면 해당 필드를 포함하거나 중첩 가능
- 즉, Serializer를 조작하여 Article에 Comment를 추가할 수 있음
```python
from rest_framework import serializers
from .models import Article, Comment


class CommentSerializer(serializers.ModelSerializer):
    class Meta:
        model = Comment
        fields = "__all__"
        read_only_fields = ("article",)


class ArticleSerializer(serializers.ModelSerializer):
    comments = CommentSerializer(many=True, read_only=True)

    class Meta:
        model = Article
        fields = "__all__"
```


### 커스텀 필드 추가하기
- `source`
	- SerializerField의 속성으로 해당 필드를 채우는데 사용하는 속성을 지정
	- 점 표기법을 이용하여 내부 속성에 접근 가능
```python
class ArticleSerializer(serializers.ModelSerializer):
    comments = CommentSerializer(many=True, read_only=True)
    # comments_count는 직접 필드를 추가해 주는 것(Django가 알아서 추가해주는 것이 아님!)
    # source 속성을 이용하여 데이터 값을 전달
    comments_count = serializers.IntegerField(source="comments.count", read_only=True)
    
    class Meta:
        model = Article
        fields = "__all__"
```


### 응답 구조만 변경하기
- **`to_representation()`**
	- Serialization 이후 보여지는 결과에 대해 자동으로 내부적으로 불리는 함수
	- 이 메서드를 override하여 커스텀 형식으로 변경 가능
```python
class CommentSerializer(serializers.ModelSerializer):
    class Meta:
        model = Comment
        fields = "__all__"
        read_only_fields = ("article",)
    
    def to_representation(self, instance):
        ret = super().to_representation(instance)
        ret.pop("article")
        return ret
```

