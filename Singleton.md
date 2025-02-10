# 2025 / 02 / 10 싱글톤

싱글톤은 프로그래밍 디자인 패턴 중 `추상 객체 인스턴스 생성 패턴` 중의 하나이다
유니티에서 많이 사용하는데,

1. 게임 시스템에서 전체를 관장하는 스크립트 (단일 시스템 자원 관리 차원)
2. 게임 시스템상 전역 변수의 역할을 하는 스크립트
3. 씬 로드시 데이터가 파괴되지 않고 유지
4. 여러 오브젝트가 접근을 해야 하는 스크립트의 역할
5. 단 한개의 객체만 존재 (게임 전체를 관장하는 스크립트가 두개 이상있으면 꼬여버림;;)

 결국 새로운 씬을 로드를 하게 되면, 앞에 있던 변수들은 전부 파괴되고 새로운 씬이 로드가 됨
하지만, 싱글톤은 씬 이동시 자신을 파괴하지 않으면서 자신이 가진 데이터들 또한 함께 유지함

예시 코드는 다음과 같다
<pre>
  <code>
    /// 구글링해서 나오는 대부분의 유니티 싱글톤 느낌
public class GameManager : MonoBehaviour
{
	// instance 멤버변수는 private하게 선언
    private static GameManager instance = null;

    private void Awake()
    {
        if (null == instance)
        {
            // 씬 시작될때 인스턴스 초기화, 씬을 넘어갈때도 유지되기위한 처리
            instance = this;
            DontDestroyOnLoad(this.gameObject);
        }
        else
        {
            // instance가, GameManager가 존재한다면 GameObject 제거 
        	Destroy(this.gameObject);
        }
    }
    
	// Public 프로퍼티로 선언해서 외부에서 private 멤버변수에 접근만 가능하게 구현
    public static GameManager Instance
    {
        get
        {
            if (null == instance)
            {
                return null;
            }
            return instance;
        }
    
출처: https://welcomeheesuk.tistory.com/62 [버거덕 게임즈:티스토리]
  </code>
</pre>
