# 2024 / 02 / 05

깃허브에 레포지토리를 따로 올려서 작성했지만, 그래도 해당 과제를 통해 코딩을 하며 직접 배웠던 것들을 다시 정리해 글로 남긴다

## 최대한 구성요소로 나누어 객체화 시켜 코드를 작성하였다. 
* 게임의 기본적인 요소를 구성하는 Program class를 작성하여 Main 함수를 불러왔다
<pre>
  <code>
    namespace TextRPG
{
    internal class Program
    {
        static void Main(string[] args) // 메인함수로, 게임의 시작입니다! 
        {
            Player player = new Player("Chad", "전사", 10, 5, 100, 1500); // 플레이어를 메인에 생성하고, 초기화합니다
            Inventory inventory = new Inventory(); // 인벤토리를 메인에 생성하고, 초기화합니다
            Store store = new Store(player); // 상점을 메인에 생성하고, 초기화합니다

            while (true) // 게임이 종료될 때까지 반복하고, 잘못된 입력이 나오면 초기화면으로 돌아가게끔 동작합니다
            {

                Console.WriteLine("스파르타 마을에 오신 여러분 환영합니다.");
                Console.WriteLine("이곳에서 던전으로 들어가기 전 활동을 할 수 있습니다\n");


                Console.WriteLine("1. 상태보기");
                Console.WriteLine("2. 인벤토리");
                Console.WriteLine("3. 상점\n");

                Console.WriteLine("원하시는 행동을 입력해주세요");
                Console.Write(">> ");

                if (int.TryParse(Console.ReadLine(), out int number)) // 입력받은 값이 숫자인지 확인하고, 해당 숫자에 따라 player, inventory, store를 호출합니다.
                {
                    {
                        if (number == 1)
                        {
                            player.ShowStatus();
                        }
                        else if (number == 2)
                        {
                            inventory.ShowInventory();
                            Console.Write("\n원하시는 행동을 입력해주세요: ");
                            string input = Console.ReadLine();
                            if (input == "1")
                            {
                                inventory.ManageEquipment();
                            }
                            else if (input == "2")
                            {
                                break;
                            }
                            else
                            {
                                Console.WriteLine("잘못된 입력입니다.");
                                Console.ReadKey();
                            }
                        }
                        else if (number == 3)
                        {
                            store.ShowStore();
                            Console.Write("\n원하시는 행동을 입력해주세요: ");
                            string input = Console.ReadLine();
                            if (input == "1")
                            {
                                store.PurchaseItem();
                            }
                            else if (input == "2")
                            {
                                break;
                            }
                            else
                            {
                                Console.WriteLine("잘못된 입력입니다.");
                                Console.ReadKey();
                            }
                        }
                        else
                        {
                            Console.WriteLine("잘못된 입력입니다. 다시 입력해주세요.\n");
                        }
                    }
                }
            }


        }
    }
}



  </code>
</pre>

그리고 item이 들어가는 항목들을 모두, 따로 item.cs 파일을 생성하여 그 안에 선언하고 생성하였다

<pre>
  <code>
    public class Item // 아이템 클래스를 생성하여, 아이템이 쓰이는 함수들을 해당 파일에 전부 정리하였습니다
{
    public string Name { get; set; } // 아이템 이름
    public string Type { get; set; } // 아이템 형식
    public int Value { get; set; } // 아이템 능력치
    public string Description { get; set; } // 아이템 설명
    public int Price { get; set; } // 아이템 가격
    public bool IsEquip { get; set; } // 아이템 장착 여부
    public bool IsPurchased { get; set; } // 아이템 구매 여부


    public Item(string name, string type, int value, string description, int price = 0) // 새로운 아이템 생성
    {
        Name = name;
        Type = type;
        Value = value;
        Description = description;
        Price = price;
        IsEquip = false; // 장착이 안된 상태로 인스턴스 초기화
        IsPurchased = false; // 구매가 안된 상태로 인스턴스 초기화
    }

    public void ToggleEquip() // 장착 상태 변경
    {
        IsEquip = !IsEquip;
    }

    public void TogglePurchase() // 구매 상태 변경
    {
        IsPurchased = !IsPurchased;
    }

}

class Inventory
{
    private List<Item> Invenitems; //아이템 목록

    public Inventory() // 인벤토리 초기화
    {
        Invenitems = new List<Item>
        {
            new Item("무쇠갑옷", "방어력", +5, "무쇠로 만들어져 튼튼한 갑옷입니다."),
            new Item("스파르타의 창", "공격력", +7, "스파르타의 전사들이 사용했다는 전설의 창입니다."),
            new Item("낡은 검", "공격력",+2, "쉽게 볼 수 있는 낡은 검 입니다.")
        };
    }

    public void ShowInventory() // 인벤토리 표시 
    {
        Console.Clear();
        Console.WriteLine("인벤토리\n보유 중인 아이템을 관리할 수 있습니다.\n");
        Console.WriteLine("[아이템 목록]");
        for (int i = 0; i < Invenitems.Count; i++)  // 아이템 목록을 List에서 참조하여 표시해줍니다 
        {
            string equipped = Invenitems[i].IsEquip ? "[E]" : "";
            Console.WriteLine($"{i + 1}. {equipped}{Invenitems[i].Name} | {Invenitems[i].Type} |{Invenitems[i].Value} | {Invenitems[i].Description}"); 
        }
        Console.WriteLine("\n1. 장착 관리\n2. 나가기");
    }

    public void ManageEquipment() // 장착 관리
    {
        while (true)
        {
            ShowInventory(); 
            Console.Write("\n원하시는 아이템 번호를 입력해주세요 (0: 종료): ");
            if (int.TryParse(Console.ReadLine(), out int choice) && choice > 0 && choice <= Invenitems.Count) // choice가 0보다 크고 아이템 목록보다 작을 때
            {
                Invenitems[choice - 1].ToggleEquip();
                Console.WriteLine($"{Invenitems[choice - 1].Name} {(Invenitems[choice - 1].IsEquip ? "장착" : "해제")} 완료!"); // 장착 상태를 변경 할 수 있습니다
            }
            else if (choice == 0)
            {
                break; // 0을 입력하면 종료
            }
            else
            {
                Console.WriteLine("잘못된 입력입니다.");
            }
            Console.ReadKey();
        }
    }
}

public class Store
{
    private List<Item> storeitems; // 상점 아이템 목록
    private Player player; // 플레이어 참조
    public Store(Player player)
    {
        storeitems = new List<Item>() // 상점 아이템 목록  // 상점 초기화
        {
            new Item("수련자 갑옷", "방어력", +5, "수련에 도움을 주는 갑옷입니다.", 1000),
            new Item("무쇠갑옷", "방어력", +9, "무쇠로 만들어져 튼튼한 갑옷입니다.",0),
            new Item("스파르타의 갑옷", "방어력", +15, "스파르타의 전사들이 사용했다는 전설의 갑옷입니다",3500),
            new Item("낡은 검", "공격력", +2, "쉽게 볼 수 있는 낡은 검 입니다.",600),
            new Item("청동 도끼", "공격력", +5, "청동으로 만들어진 도끼입니다.",1500),
            new Item("스파르타의 창", "공격력", +7, "스파르타의 전사들이 사용했다는 전설의 창입니다.",0)
        };
        this.player = player;
    }

    public void ShowStore() // 상점 표시
    {
        Console.Clear();
        Console.WriteLine("상점\n필요한 아이템을 얻을 수 있는 상점입니다.\n");
        Console.WriteLine("[보유 골드]");
        Console.WriteLine($"Gold: {player.Gold}G\n");
        Console.WriteLine("[아이템 목록]");
        for (int i = 0; i < storeitems.Count; i++) // 상점 아이템 목록을 List에서 참조하여 표시해줍니다
        {
            string priceText = storeitems[i].IsPurchased ? "구매완료" : $"{storeitems[i].Price}G";
            Console.WriteLine($"{i + 1}. {storeitems[i].Name} | {storeitems[i].Type} | {storeitems[i].Value} | {storeitems[i].Description} | {priceText}"); // priceText는 구매완료 또는 가격을 표시합니다
        }
        Console.WriteLine("\n1. 아이템 구매하기\n2. 나가기");
    }

    public void PurchaseItem() // 아이템 구매
    {
        while (true)
        {
            ShowStore();
            Console.Write("\n원하시는 아이템 번호를 입력해주세요 (0: 종료): ");
            if (int.TryParse(Console.ReadLine(), out int choice) && choice > 0 && choice <= storeitems.Count)
            {
                if (storeitems[choice - 1].Price <= 800) // 골드를 차감해서 구매할 수 있는 코드를 작성하고 싶은데 잘 모르겠습니다...
                {
                    storeitems[choice - 1].TogglePurchase();
                    Console.WriteLine($"{storeitems[choice - 1].Name} 구매 완료!");
                }
                else
                {
                    Console.WriteLine("골드가 부족합니다.");
                }
            }
            else if (choice == 0)
            {
                break;
            }
            else
            {
                Console.WriteLine("잘못된 입력입니다.");
            }
            Console.ReadKey();
        }
    }

}



  </code>
</pre>

마지막으로 Player.cs를 생성하여 그 안에 플레이어가 상호작용 하는 클래스와 메서드를 만들어주었다


<pre>
  public class Item // 아이템 클래스를 생성하여, 아이템이 쓰이는 함수들을 해당 파일에 전부 정리하였습니다
{
    public string Name { get; set; } // 아이템 이름
    public string Type { get; set; } // 아이템 형식
    public int Value { get; set; } // 아이템 능력치
    public string Description { get; set; } // 아이템 설명
    public int Price { get; set; } // 아이템 가격
    public bool IsEquip { get; set; } // 아이템 장착 여부
    public bool IsPurchased { get; set; } // 아이템 구매 여부


    public Item(string name, string type, int value, string description, int price = 0) // 새로운 아이템 생성
    {
        Name = name;
        Type = type;
        Value = value;
        Description = description;
        Price = price;
        IsEquip = false; // 장착이 안된 상태로 인스턴스 초기화
        IsPurchased = false; // 구매가 안된 상태로 인스턴스 초기화
    }

    public void ToggleEquip() // 장착 상태 변경
    {
        IsEquip = !IsEquip;
    }

    public void TogglePurchase() // 구매 상태 변경
    {
        IsPurchased = !IsPurchased;
    }

}

class Inventory
{
    private List<Item> Invenitems; //아이템 목록

    public Inventory() // 인벤토리 초기화
    {
        Invenitems = new List<Item>
        {
            new Item("무쇠갑옷", "방어력", +5, "무쇠로 만들어져 튼튼한 갑옷입니다."),
            new Item("스파르타의 창", "공격력", +7, "스파르타의 전사들이 사용했다는 전설의 창입니다."),
            new Item("낡은 검", "공격력",+2, "쉽게 볼 수 있는 낡은 검 입니다.")
        };
    }

    public void ShowInventory() // 인벤토리 표시 
    {
        Console.Clear();
        Console.WriteLine("인벤토리\n보유 중인 아이템을 관리할 수 있습니다.\n");
        Console.WriteLine("[아이템 목록]");
        for (int i = 0; i < Invenitems.Count; i++)  // 아이템 목록을 List에서 참조하여 표시해줍니다 
        {
            string equipped = Invenitems[i].IsEquip ? "[E]" : "";
            Console.WriteLine($"{i + 1}. {equipped}{Invenitems[i].Name} | {Invenitems[i].Type} |{Invenitems[i].Value} | {Invenitems[i].Description}"); 
        }
        Console.WriteLine("\n1. 장착 관리\n2. 나가기");
    }

    public void ManageEquipment() // 장착 관리
    {
        while (true)
        {
            ShowInventory(); 
            Console.Write("\n원하시는 아이템 번호를 입력해주세요 (0: 종료): ");
            if (int.TryParse(Console.ReadLine(), out int choice) && choice > 0 && choice <= Invenitems.Count) // choice가 0보다 크고 아이템 목록보다 작을 때
            {
                Invenitems[choice - 1].ToggleEquip();
                Console.WriteLine($"{Invenitems[choice - 1].Name} {(Invenitems[choice - 1].IsEquip ? "장착" : "해제")} 완료!"); // 장착 상태를 변경 할 수 있습니다
            }
            else if (choice == 0)
            {
                break; // 0을 입력하면 종료
            }
            else
            {
                Console.WriteLine("잘못된 입력입니다.");
            }
            Console.ReadKey();
        }
    }
}

public class Store
{
    private List<Item> storeitems; // 상점 아이템 목록
    private Player player; // 플레이어 참조
    public Store(Player player)
    {
        storeitems = new List<Item>() // 상점 아이템 목록  // 상점 초기화
        {
            new Item("수련자 갑옷", "방어력", +5, "수련에 도움을 주는 갑옷입니다.", 1000),
            new Item("무쇠갑옷", "방어력", +9, "무쇠로 만들어져 튼튼한 갑옷입니다.",0),
            new Item("스파르타의 갑옷", "방어력", +15, "스파르타의 전사들이 사용했다는 전설의 갑옷입니다",3500),
            new Item("낡은 검", "공격력", +2, "쉽게 볼 수 있는 낡은 검 입니다.",600),
            new Item("청동 도끼", "공격력", +5, "청동으로 만들어진 도끼입니다.",1500),
            new Item("스파르타의 창", "공격력", +7, "스파르타의 전사들이 사용했다는 전설의 창입니다.",0)
        };
        this.player = player;
    }

    public void ShowStore() // 상점 표시
    {
        Console.Clear();
        Console.WriteLine("상점\n필요한 아이템을 얻을 수 있는 상점입니다.\n");
        Console.WriteLine("[보유 골드]");
        Console.WriteLine($"Gold: {player.Gold}G\n");
        Console.WriteLine("[아이템 목록]");
        for (int i = 0; i < storeitems.Count; i++) // 상점 아이템 목록을 List에서 참조하여 표시해줍니다
        {
            string priceText = storeitems[i].IsPurchased ? "구매완료" : $"{storeitems[i].Price}G";
            Console.WriteLine($"{i + 1}. {storeitems[i].Name} | {storeitems[i].Type} | {storeitems[i].Value} | {storeitems[i].Description} | {priceText}"); // priceText는 구매완료 또는 가격을 표시합니다
        }
        Console.WriteLine("\n1. 아이템 구매하기\n2. 나가기");
    }

    public void PurchaseItem() // 아이템 구매
    {
        while (true)
        {
            ShowStore();
            Console.Write("\n원하시는 아이템 번호를 입력해주세요 (0: 종료): ");
            if (int.TryParse(Console.ReadLine(), out int choice) && choice > 0 && choice <= storeitems.Count)
            {
                if (storeitems[choice - 1].Price <= 800) // 골드를 차감해서 구매할 수 있는 코드를 작성하고 싶은데 잘 모르겠습니다...
                {
                    storeitems[choice - 1].TogglePurchase();
                    Console.WriteLine($"{storeitems[choice - 1].Name} 구매 완료!");
                }
                else
                {
                    Console.WriteLine("골드가 부족합니다.");
                }
            }
            else if (choice == 0)
            {
                break;
            }
            else
            {
                Console.WriteLine("잘못된 입력입니다.");
            }
            Console.ReadKey();
        }
    }

}

</pre>

## 결론
아쉬웠던 점
- 상점 내에서 아이템을 사고 팔때, 플레이어가 소지하고 있는 골드를 소비하고 나타내게 하고 싶었지만,
  시간 내 기능구현에 실패하였다
- 콘솔 창에서 진행되는 게임이라 그런지, `Console.Clear();`를 사용했음에도 불구하고, 가독성이 좋지 않아
  아스키 코드를 통해, 수정해야겠다는 생각이 들었다
  실제 유니티에서 작동하듯이, 씬매니저를 하나 만들어서 구축하는 것도 방법이라고 생각하였다

좋았던 점
- 생으로 코딩을 하여 게임을 만드는 경험을 하는건, 보통 의지가 아니면 할 수 없는데, 반강제로 하게되서
  의외로 재미있고 도전적인 경험이었다
- 한정적인 상황에서, 극한의 효율을 뽑아낼수 있는 경험을 만들어 주는거 같아, 짜고 나서 뿌듯한 느낌이 들었다
