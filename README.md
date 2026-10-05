# lunote-deploy

LUNOTE staging 워크로드의 **배포본**. ArgoCD의 `api-staging` 앱이 이 저장소의 `staging` 브랜치를 읽는다.

- 사람이 직접 고치는 곳이 아니다. 원본은 [So1ky/LUNOTE](https://github.com/So1ky/LUNOTE)의 `infra/k8s/workloads/api/`이고,
  CI(Jenkins)가 빌드한 커밋의 그 디렉터리를 `workloads/api/`로 통째로 복사한 뒤 `overlays/staging/kustomization.yaml`의
  `newTag`만 바꿔 커밋한다. 여기서 고친 내용은 다음 배포 때 덮인다.
- 커밋 하나가 배포 한 번이다: `chore(deploy): staging API 이미지 <이미지 SHA> (소스 <앱 저장소 SHA>)`.
- CI의 deploy key는 이 저장소에만 쓸 수 있다. 앱 저장소에는 등록돼 있지 않다.
- GitHub Actions는 꺼 둔다. 켜지 않는다.

## 롤백

최신 커밋을 revert해 push하면 ArgoCD가 이전 이미지와 매니페스트로 되돌린다.

```bash
git clone -b staging git@github.com:So1ky/lunote-deploy.git && cd lunote-deploy
git revert --no-edit HEAD && git push origin staging
```

DB 마이그레이션은 되돌려지지 않는다. 자세한 절차는 앱 저장소의 `infra/k8s/README.md`.
