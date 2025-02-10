# 2025 / 02 / 04

본격적으로 Text RPG를 제작하는 날이다

해당 과제에서 요구하는 사항은 다음과 같다
1. 게임 시작 화면 구성하기
2. 플레이어의 상태보기
3. 플레이어의 인벤토리 표시
4. 인벤토리의 장착관리
5. 상점 시스템 구현
6. 상점에서 아이템 구매

현 시간 기준으로 6가지의 과제 중에서 내가 내 힘으로 해결한 과제는 1번과 2번이다!

## 게임 시작 화면 구성 
일단, 시작 화면에 들어갈 요소들은 다음과 같다,

시작 화면 소개 문구와, 

던전으로 들어가기 전 활동 구성하기 해당 동작을 
1. 상태보기
2. 인벤토리
3. 상점

으로 나눈뒤, 원하는 행동을 입력하여 해당 화면으로 넘어가는 거 까지 구현을 하는 것이다
<pre>
  <code>
    Console.WriteLine("스파르타 마을에 오신 여러분 환영합니다.");
    Console.WriteLine("이곳에서 던전으로 들어가기 전 활동을 할 수 있습니다\n");


    Console.WriteLine("1. 상태보기");
    Console.WriteLine("2. 인벤토리");
    Console.WriteLine("3. 상점\n");

    Console.WriteLine("원하시는 행동을 입력해주세요");
    Console.Write(">> ");
  </code>
</pre>

요렇게 기본 화면 세팅을 해주었다 그리고
<pre>
  <code>
    while (true)
    {
    
    }
  </code>
</pre>

를 출력코드 위에 끼워 붙여서, 1, 2, 3 번이 아닌 다른 입력값이 들어오면 다시 입력할 수 있게끔 적용하였다

그리고 해당 화면으로 넘어가는 구성은
<pre>
  <code>
    if (int.TryParse(Console.ReadLine(), out int number))
  {
    if (number == 1)
    {
      Console.WriteLine("상태보기");
    }
    else if (number == 2)
    {
      Console.WriteLine("인벤토리");
    }
    else if (number == 3)
    {
        Console.WriteLine("상점");
    }
    else
    {
        Console.WriteLine("잘못된 입력입니다. 다시 입력해주세요.\n");
    }
  }
  </code>
</pre>

`int.TryParse(Console.ReadLine(), out int number` 코드를 사용하여, readline으로 받은 값을 정수 값인지 그 값에 따라서 판단하게 만들었다

## 플레이어의 상태보기
이제 플레이어의 정보를 입력받아, 출력해주는 코드를 작성해야 한다!
그래서 `Player.cs`파일을 따로 프로젝트 내에서 생성한뒤, public class로 Player를 만들어줬다
<pre>
  <code>
    public class Player
  {
    public int Lv {  get; set; }
    public string Name { get; set; }
    public string Class {  get; set; }
    public int Atk { get; set; }
    public int Def {  get; set; }
    public int Hp { get; set; }
    public int Gold {  get; set; }
  }
  </code>
</pre>
Player에 들어갈 필드값들을 할당해주고!
<pre>
  <code>
    public void ShowStatus( )
  {
    Console.WriteLine("상태 보기");
    Console.WriteLine("캐릭터의 정보가 표시됩니다." );

    Console.WriteLine("\n====================");
    Console.WriteLine($"Lv. {Lv}");
    Console.WriteLine($"{Name} ( {Class} )");
    Console.WriteLine($"공격력 : {Atk}");
    Console.WriteLine($"방어력 : {Def}");
    Console.WriteLine($"체 력 : {Hp}");
    Console.WriteLine($"Gold : {Gold} G");
    Console.WriteLine("====================\n");
    Console.WriteLine("0. 나가기\n");

    Console.Write("원하시는 행동을 입력해주세요.\n>> ");
    Console.ReadLine();
  }
  </code>
</pre>
정보를 표시해주는 ShowStatus( ) 함수를 만들어주었다

<pre>
  <code>
    public void InputStatus()
  {
    Console.WriteLine("캐릭터의 레벨, 이름, 직업, 공격력, 방어력, 체력, 초기 소지 골드양을 입력합니다!\n");
   
    Console.Write("Lv: ");
    int Lv = int.Parse(Console.ReadLine());
    Console.Write("Name");
    Name = Console.ReadLine();
    Console.Write("Class: ");
    Class = Console.ReadLine();
    Console.Write("ATK: ");
    Atk = int.Parse(Console.ReadLine());
    Console.Write("DEF: ");
    Def = int.Parse(Console.ReadLine());
    Console.Write("HP: ");
    Hp = int.Parse(Console.ReadLine());
    Console.Write("Gold: ");
    Gold = int.Parse(Console.ReadLine());
  }
  </code>
</pre>
직접 Status 값을 넣어서 표시해주는 함수도 한번 넣어봤다...
하지만 다음 과제를 생각하면 사족이었다!
