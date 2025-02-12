# StoreScene 마저 구현하기!
저번 TIL에서 작성한 코드를 몇 가지 기능을 추가하였다
1. 아이템을 사고 팔때, 아이템이 구매되면 상점에 "판매완료" 문구가 뜨도록 변경하였다
2. 포션을 사용할 수 있는 메서드를 ItemManager.cs에 추가하였다

## 아이템을 사고 파는 SoldOutItem 메서드 작성
<pre>
  <code>
    private void SoldOutItem(int i)
  {
      var item = ItemManager.Instance.equipmentList[i];

   if (ItemManager.Instance.IsOwned(item))
          {
         Console.Write("\x1b[38;2;255;255;0m판매완료\x1b[0m");
          }
          else
          {
              Console.Write($"{item.Price}G");
          }
  }
  </code>
</pre>

해당 메서드를 만들어서, storeitemlist에서 바로 아이템을 받아오면서 값을 StoreScene내에서 변경하는 것이 가능했다
처음에 if문을 좀 더 간결하게 적어볼까 생각도 했지만...아직까지 뾰족한 방법이 생각나지 않았다...
출력은 이런식으로 동작하게 만들었다,

<pre>
  <code>
Console.Clear();
Console.WriteLine("상점에 들어왔습니다!");
Console.WriteLine($"[보유 골드] {DataManager.Instance.player.data.gold}G\n");
Console.WriteLine("구매할 아이템을 선택하세요!\n");

var storeItems = ItemManager.Instance.equipmentList;

for (int i = 0; i < storeItems.Count; i++)
{
    var item = storeItems[i];
    Console.ForegroundColor = ConsoleColor.Cyan;
    Thread.Sleep(150);
    Console.Write($"{i + 1}. {item.Name} | 종류: {item.ItemText} | 직업: {item.ClassText} | 공격력: {item.Attack} | 방어력: {item.Defence} | {item.Description} | ");
    SoldOutItem(i);
    Console.WriteLine();
    Console.ResetColor();
}
  </code>
</pre>

## 포션을 먹으면, Player에 적용되는 메서드 작성

<pre>
  <code>
  public void UsePotion(Equipment potion)
  {
     var player = DataManager.Instance.player;

     if(potion != null)
     {
         if (ItemManager.Instance.ownedList.Contains(potion))
         {
             ItemManager.Instance.ownedList.Remove(potion);
             if(potion is Potion)
             {
                 potion = null;
                 Console.WriteLine($"{potion.Name}을 마셨습니다.");
                 player.data.hp += potion.Hp;
                 player.data.mp += potion.Mp;
             }
             else
             {
                 Console.WriteLine("포션을 선택해주세요!");
             }
         }
     }
  }
  </code>
</pre>

현재 데이터 매니저에서 작성한 플레이어의 체력과 마나를 가져와, 해당 ownedList에 있는 값을 참조하여 플레이어가 해당 포션이 가지고 있는

회복량을 더해주는 방식으로 동작하게 작성하였다!

- 참조를 통해서, 데이터를 자유롭게 가져다가 쓸 수 있어서 코드 작성하는 것이 수월했다 (마치 라이브러리에서 갖다가 쓰는 느낌)
- 게임의 등장하는 요소들, 특히 다른 장면에서 재사용성이 높은 오브젝트들은 다른 프로젝트를 진행할때도 자유롭게 가지고 있는 값을 쓸 수 있게 정리해야겠다
- 오늘도 쓰면서, 객체 지향의 위대함을 느낄수 있었다...
