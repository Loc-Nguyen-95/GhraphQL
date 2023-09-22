# GraphQL
#### Giới thiệu

GraphQl là 1 diều kiện tốt để giao tiếp giữa FE và BE 

Tất cả chi tiết và khả năng được trình bày trong GraphQL Schema, để viết function về schema bạn cần tìm hiểu về GraphQL Type Schema trước 

Trong bài viết này bạn sẽ được biết về GraphQL types: 5 built-in Scalar types, Enums, List and Non-null wrapping types, Object types, abstract Interface & Union types ➡  Build a complete GraphQL schema 

## Prerequisites - ĐK tiên quyết 
- Hiểu cơ bản về GraphQL
- [Set up 1 GraphQL API server in NodeJs](https://www.digitalocean.com/community/tutorials/how-to-set-up-a-graphql-api-server-in-node-js)
## Scalar Types (Vô hướng)
GraphQL schema cuối cùng để giải quyết nhiều loại scalar types (Primative value)

GraphQL response có là đại diện cho 1 cái cây, và scalar types như những cái lá cuối cùng của cây (last level always resolve scalar (or enum) type

5 built-in scalar types : Int, Float, String, Boolean và ID 

#### Custom scalars 
Bạn có thể sử dụng custom scalar để tạo types - cho server-level validation (Date, Time, Url) 
VD 
```swift
  scalar Date
```
## Enum Type 
Mô tả 1 set của possitive values
VD
```swift
"The job class of the character."
enum Job {
  FIGHTER
  WIZARD
}

"The species or ancestry of the character."
enum Species {
  HUMAN
  ELF
  DWARF
}
```
Bằng cách này có thể đảm bảo Job là FIGHTER hay WIZARD, nó sẽ không thể nếu như bạn dùng String type thay vì tạo 1 custom Enum (được viết hoa toàn bộ)

Enums cũng có thể được sử dụng như 1 giá trị của 1 arguments (VD trong 1 object)

```swift
enum Hand {
  SINGLE
  DOUBLE
}

"A valiant weapon wielded by a fighter."
type Weapon {
  name: String!
  attack: Int
  range: Int
  hand: Hand
}

type Query {
  weapons(hand: Hand = SINGLE): [Weapon]
}
```
SINGLE (default)
## Non-Nulll Type
Nên chú ý rằng `null` hay `undefined` - được coi là Primitive, nhưng lại không có trong built-in scalar. Không tồn tại trong GraphQL

Tất cả những type trong GraphQL đều có thể là null (by default) và vì thế null là 1 phản hồi có giá trị cho mọi type

Để tạo nên 1 yêu cầu về giá trị, nó phải được chuyển sang GraphQL Non-Null Type với dấu chấm than ở cuối 

Non-null định nghĩa 1 type sửa đổi, được sử dụng để sửa đổi 1 loại đang được đề cặp đến
VD 
`string` có thể là tuỳ chọn (hoặc null) , còn `string!` thì được yêu cầu (non-null)
## List Type 
List type là một loại khác của type modifier, mọi loại (type) được bao bởi dấu ngoặc vuông "[]" đều có thể trở thành List type - là 1 collection định nghĩa type của mỗi item trong danh sách
VD, type được định nghĩa [Int] sẽ là 1 collection của Int type, và [String] sẽ là collection của String type. Non-null và List có thể được dùng cùng với nhau để tạo nên 1 laoij dùng cả trong yêu cầu và định nghĩa, VD [String]!
## Object Type 
Nếu như GraphQL scalar type mô tả "lá" của cấu trúc GraphQL response, Object type mô tả "nhánh", hầu hết các schema đều là Object 

Objects bao gồm danh sách các field (key) được đặt tên và value được xử lí 

Được định nghĩa với từ khoá `type`, ít nhất 1 hay nhiều field cần được định nghĩa, và 1 field có thể bắt đầu với 2 dấu gạch dưới "__" để tránh conflic với GraphQL system

VD: cũng có thể Fighter object để định nghĩa cho các đặc tính trong game
```swift
"A hero with direct combat ability and strength."
type Fighter {
  id: ID!
  name: String!
  level: Int
  active: Boolean!
}
```
Trong VD trên, Fighter Object được khai báo và có 4 trường được đặt tên là : 
- id : Non-null ID type
- name : Non-null String type
- level: Int type
- active: Non-null boolean type

Sự khai báo trên bạn có thể comment bằng dấu ngoặc kép "" 
VD: "A hero with direct combat ability and strength."
VD trên, mỗi field xử lí 1 scalar type, nhưng Object field có thể xử lí Object field khác. VD bạn có thể tạo `Weapon` type, và GraphQL schema sẽ được set up ở nơi mà weapon field trong Fighter sẽ được xử lí 
VD 
```swift
"A valiant weapon wielded by a fighter."
type Weapon {
  name: String!
  attack: Int
  range: Int
}

"A hero with direct combat ability and strength."
type Fighter {
  id: ID!
  name: String!
  level: Int
  active: Boolean!
  weapon: Weapon
}
```
Object có thể được lồng trong 1 field của Object khác 

## Root Operation Types 
Có 3 loại Object đặc biệt trong GraphQL schema : Query, Mutation, Subscription (được biết như Root Operator type) và tuân thủ mọi rules như những Object type khác

Từ khoá schema đại diện cho điểm truy cập vào GraphQL schema. Your root Query, Mutation, Subcription sẽ nằm trong root schema Object
```swift
  schema {
  query: Query
  mutation: Mutation
  subscription: Subscription
}
```
The Query type được yêu cầu trong mọi GraphQL schema đại diện cho read request, tương tự như REST API GET. VD sau về root Query Object sẽ trả về List of Fighter types:
```swift
type Query {
  fighters: [Fighter]
}
```
Mutation đại diện cho write request, tương tự như POST, PUT, DELETE trong REST API. Trong VD sau Mutation có 1 trường addFighter với 1 input được đặt tên 
```swift
type Mutation {
  addFighter(input: FighterInput): Fighter
}
```
Cuối cùng, Subcription tương ứng với event stream, được sử dụng trong liên kết với Wedsocket trong web app

VD
```swift
type Subscription {
  randomBattle(enemy: Enemy): BattleResult
}
```
## Field Arguments 
Field của GraphQL Object là 1 function thiết yếu trả về giá trị, có thể chấp nhận đối số là những function. Field argument thì được định nghĩa bởi tên của type. Argument có thể là non-object type. trong VD sau Fighter Object có thể được lọc bởi trường id (resolve to a Non-null ID type)
```swift
type Query {
  fighter(id: ID!): Fighter
}
```
Được dùng để fetch single item từ data store, nhưng argument có thể dùng cho filtering, phân trang, và những truy vấn đặc biệt khác 

## Interface Type
Giống như Object type, Interface type bao gồm list tên và value type tương ứng, gần giống như object nhưng là tập con của object

Như VD chúng ta có Fighter Object nhưng chúng ta cũng muốn tạo những object khác như Wizard, Healer có chung 1 vài trường nhưng cũng có những điểm khác. Trong trường hợp này bạn có thể sử dụng Interface để định nghĩa trường chung, và tạo 1 object là triển khai cuả Interface 
VD 
```swift
"A hero on a quest."
interface BaseCharacter {
  id: ID!
  name: String!
  level: Int!
  species: Species
  job: Job
}
```
Mỗi character type sẽ có trường id, name, level, species, job
Bây giờ, hãy tưởng tượng bạn có type Fighter type Wizard sẽ có những trường chung, nhưng Fighter sử dụng Weapon và Wizard sử dụng Spell. Bạn có thể sử dụng từ khoá implemens để khoanh vùng mỗi khi triển khai BaseCharacter, có nghĩa là nó sẽ có tất cả các trường được tạo bởi Interface 
```swift
"A hero with direct combat ability and strength."
type Fighter implements BaseCharacter {
  id: ID!
  name: String!
  level: Int!
  species: Species
  job: Job!
  weapon: Weapon
}

"A hero with a variety of magical powers."
type Wizard implements BaseCharacter {
  id: ID!
  name: String!
  level: Int!
  species: Species
  job: Job!
  spells: [Spell]
}
```
Fighter và Wizard đều là những triển khai có giá trị của BaseCharacter interface bởi vì nó có những tập con bắt buộc 

## Union Type
Một loại trích xuất khác được sử dụng với Object là Union type. Sử dụng từ khoá `union` bạn có thể định nghĩa type với list của Object

Sử dụng interface được tạo ra ở phần trước, bạn cũng có thể tạo Character Union để định nghĩa character như Wizard hay Fighter 
VD 
```swift
union Character = Wizard | Fighter
```
Dấu bằng thể hiện sự định nghĩa, và dấu | thể hiện OR statement. Chú ý, Union phải bao gồm Object hay Interfaces. Scalar type không có giá trị trong Union

Bây giờ nếu như bạn truy vấn list character, nó có thể sử dụng Character Union và trả về Wizard và Fighter type
#### Kết luận 
