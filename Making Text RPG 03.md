# StoreScene 편집하기
1. 하루종일 내가 맡은 StoreScene을 편집하였다, 혼자 코드를 작성하다보니, 오류가 많았다
2. 변수나 구조체에서 선언한 값들을 놓치고 참조하여, 오류가 생기거나, `null`로 출력되는 일이 잦았다

다음은 내가 작성한 StoreScene이다
<pre>
  <code>
    using SpartaDungeon.Managers;

namespace SpartaDungeon.Scenes
{
    public class StoreScene : BaseScene
    {
        #region 새로운 생성자 만들기 금지
        // 생성자에서는 현재 씬의 이름만 설정한다. 씬에 있는 멤버들의 초기화는 Awake나 Start에서 한다
        public StoreScene(string name) : base(name) { }
        #endregion
        //
        public override void Awake()
        {
            ItemManager.Instance.CreateItem("낡은 갑옷", 2, "방어구", 1, " 전사 ", 0, 50, 0, 0, "낡았지만 아직은 쓸만하다", 150);
            ItemManager.Instance.CreateItem("빛바랜 검", 1, "무기", 1, " 전사 ", 30, 0, 0, 0, "색이 빠진 낡은 검, 겨우 휘두룰수 있는 정도다", 100);
            ItemManager.Instance.CreateItem("희미한 마력의 로브", 2, "방어구", 2, " 마법사 ", 0, 40, 0, 0, "가죽 덩어리 같지만, 희미한 마력이 느껴진다", 150);
            ItemManager.Instance.CreateItem("편백나무 막대기", 1, "무기", 2, " 마법사 ", 70, 0, 0, 0, "피톤치드가 나오는 그럭저럭한 막대기", 100);
            ItemManager.Instance.CreateItem("가죽 갑옷", 2, " 방어구 ", 3, " 궁수 ", 0, 35, 0, 0, "가볍고 꽤 질긴 가죽으로 만든 갑옷", 150);
            ItemManager.Instance.CreateItem("참나무 곡궁", 1, " 무기 ", 3, " 궁수 ", 90, 0, 0, 0, "조금 썩었지만, 가볍고 튼튼한 활", 100);
            ItemManager.Instance.CreateItem("소량의 체력 포션", 3, "포션", 3, "모두", 0, 0, 10, 0, "즉시 체력 10을 회복합니다", 30);
            ItemManager.Instance.CreateItem("소량의 마나 포션", 3, "포션", 3, "모두", 0, 0, 0, 10, "즉시 마나 10을 회복합니다", 30);

        }
        public override void Start()
        {

        }
        public override void Update()
        {
            Console.Clear();
            Console.WriteLine("상점에 들어왔습니다!");
            Console.WriteLine($"[보유 골드] {DataManager.Instance.player.data.gold}G\n");
            Console.WriteLine("구매할 아이템을 선택하세요!\n");

            var storeItems = ItemManager.Instance.equipmentList;

            for (int i = 0; i < storeItems.Count; i++)
            {
                var item = storeItems[i];
                Console.ForegroundColor = ConsoleColor.Cyan;
                Console.WriteLine();
                Thread.Sleep(100);
                Console.WriteLine($"{i + 1}. {item.Name} | 종류: {item.ItemText} | 직업: {item.ClassText} | 공격력: {item.Attack} | 방어력: {item.Defence} | {item.Description} | {item.Price}G");
                Console.ResetColor();
            }

            Console.WriteLine("\n1. 아이템 구매하기");
            Console.WriteLine("2. 아이템 판매하기");
            Console.WriteLine("3. 상점 나가기");

            Console.Write("\n선택: ");
            if (!int.TryParse(Console.ReadLine(), out int choice))
            {
                Console.WriteLine("잘못된 입력입니다. 다시 선택해주세요.");

            }

            if (choice == 3)
            {
                Console.WriteLine("상점을 나갑니다.");
                Console.ResetColor();
                SceneManager.Instance.LoadScene("town");

            }
            else if (choice == 1)
            {
                BuyItem();
            }
            else if (choice == 2)
            {
                SellItem();
            }
            else
            {
                Console.WriteLine("잘못된 선택입니다.");
            }
        }

        private void BuyItem()
        {
            Console.Write("\n구매할 아이템 번호를 입력하세요: ");
            if (!int.TryParse(Console.ReadLine(), out int itemIndex) || itemIndex < 1 || itemIndex > ItemManager.Instance.equipmentList.Count)
            {
                Console.WriteLine("잘못된 입력입니다.");
                return;
            }

            var item = ItemManager.Instance.equipmentList[itemIndex - 1];
            var player = DataManager.Instance.player;

            if (ItemManager.Instance.IsOwned(item))
            {
                Console.WriteLine("이미 구매한 아이템입니다!");
            }
            else if (player.data.gold >= item.Price)
            {
                player.data.gold -= item.Price;
                player.ownedList.Add(item);

                Console.WriteLine($"{item.Name}을 구매하였습니다!");
                Console.WriteLine($"남은 골드: {player.data.gold}G");
            }
            else
            {
                Console.WriteLine("골드가 부족합니다!");
            }

            Console.ReadLine();
        }

        private void SellItem()
        {
            var player = DataManager.Instance.player;

            if (player.ownedList.Count == 0)
            {
                Console.WriteLine("판매할 아이템이 없습니다.");
                return;
            }

            Console.WriteLine("\n[보유 중인 아이템 목록]");
            for (int i = 0; i < player.ownedList.Count; i++)
            {
                Console.WriteLine($"{i + 1}. {player.ownedList[i].Name} | 판매 가격: {player.ownedList[i].Price / 2}G");
            }

            Console.Write("\n판매할 아이템 번호를 입력하세요: ");
            if (!int.TryParse(Console.ReadLine(), out int itemIndex) || itemIndex < 1 || itemIndex > player.ownedList.Count)
            {
                Console.WriteLine("잘못된 입력입니다.");
                return;
            }

            var item = player.ownedList[itemIndex - 1];
            player.ownedList.Remove(item);
            player.data.gold += item.Price / 2;

            Console.WriteLine($"{item.Name}을 {item.Price / 2}G에 판매하였습니다!");
            Console.WriteLine($"현재 골드: {player.data.gold}G");
            Console.ReadLine();
        }

    }
    
} 
  </code>
</pre>

해당 스토어씬은 ItemManager.cs 파일과 Item.cs 파일의 값들을 많이 참조해서 적었는데 해당 코드는...

<pre>
  <code>
    namespace SpartaDungeon
{
    public interface ItemData
    {
        string Name { get; }
        int ItemType { get; }   // 1: 무기 2: 갑옷, 3: 포션, 
        // string itemText { get {switch문을 작성} }
        // 기존의 switch문(확장 용이)
        string ItemText
        {
            get
            {
                switch (ItemType)
                {
                    case 1: return "무기";
                    case 2: return "갑옷";
                    case 3: return "포션";
                    default: return "알 수 없음";
                }
            }
        }
        int ClassType { get; }   // 1: 전사 2: 마법사 3: 궁수 4: ALL
        // switch 표현식 사용(간결한 방법)
        string classText => ClassType switch
        {
            1 => "전사",
            2 => "마법사",
            3 => "궁수",
            _ => "캐릭터 직업이 없습니다(다시 불러오기)"
        };

        float Attack { get; }
        float Defence { get; }
        float Hp { get; }
        float Mp { get; }
        string Description { get; }
        int Price { get; }
    }

    public abstract class Equipment : ItemData
    {
        public string Name { get; set; }
        public int ItemType { get; set; }
        public string ItemText { get; set; }
        public int ClassType { get; set; }
        public string ClassText { get; set; }
        public float Attack { get; set; }
        public float Defence { get; set; }
        public float Hp { get; set; }
        public float Mp { get; set; }
        public string Description { get; set; }
        public int Price { get; set; }
        public bool IsEquipable { get; set; }


    }
    public class Armor : Equipment // 정확하게 명시해서 작성할것
    {
        public Armor(string name, int itemtype, string itemtext,int classtype, string classtext,float attack, float defense, float hp, float mp, string description, int price)
        {
            Name = name;
            ItemType = itemtype;
            ItemText = itemtext;
            ClassType = classtype;
            ClassText = classtext;
            Attack = attack;
            Defence = defense;
            Hp = hp;
            Mp = mp;
            Description = description;
            Price = price;
        }
    }

    public class Weapon : Equipment
    {
        public Weapon(string name, int itemtype, string itemtext,int classtype, string classtext,float attack, float defense, float hp, float mp, string description, int price)
        {
            Name = name;
            ItemType = itemtype;
            ItemText = itemtext;
            ClassType = classtype;
            ClassText = classtext;
            Attack = attack;
            Defence = defense;
            Hp = hp;
            Mp = mp;
            Description = description;
            Price = price;
        }
    }

    public class Potion : Equipment
    {
        public Potion(string name, int itemtype, string itemtext,int classtype, string classtext, float attack, float defense, float hp, float mp, string description, int price)
        {
            Name = name;
            ItemType = itemtype;
            ItemText = itemtext;
            ClassType = classtype;
            ClassText = classtext;
            Attack = attack;
            Defence = defense;
            Hp = hp;
            Mp = mp;
            Description = description;
            Price = price;
        }
    }
}
  </code>
</pre>

해당 Item.cs 코드를 통해서, 인터페이스에도 get안에서 switch문을 활용할 수 있다는 법을 배울수 있는 기회가 되었다!!!

그리고 다시한번 선언하고, 참조 변수 값을 깔끔하게 확인하고, 다른 사람이 나의 코드를 읽었을 때 (즉 협업하는 경우가 생길때)

이름을 설정하고, 참조값을 선언하는게 중요하다는 것을 명심하게 되는 경험이었다

특히나, 값이 너무 빨리 출력되어서 `Console.Readline()`을 활용하여, 천천히 값이 출력되게 하거나
`Thread Sleep()`을 이용해서 글자를 천천히 출력하여, 게임같은 아날로그 적인 느낌을 주니, 정말 게임이구나 라는 생각이 들고, 재미있었다...꿀잼...
