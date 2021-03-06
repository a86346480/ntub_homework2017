class Customer
  attr_reader :name

  #初始化，當Customer被實體化時執行
  def initialize(name)
    @name    = name
    @rentals = []
  end

  #Customer裡的方法，被賦予的物件能執行
  def add_rental(arg)
    @rentals << arg
  end

  def statement
    total_amount = 0
    frequent_renter_points = 0
    result = "Rental Record for #{@name}\n"

    #計算point
    #將@rentals陣列中的值逐一取出計算
    @rentals.each do |element|
      this_amount = 0
      case element.movie.price_code
      when Movie::REGULAR
        this_amount += 2
        this_amount += (element.days_rented - 2) * 1.5 if element.days_rented > 2
      when Movie::NEW_RELEASE
        this_amount += element.days_rented * 3
      when Movie::CHILDRENS
        this_amount += 1.5
        this_amount += (element.days_rented - 3) * 1.5 if element.days_rented > 3
      end

      frequent_renter_points += 1
      if element.movie.price_code == Movie::NEW_RELEASE && element.days_rented > 1
        frequent_renter_points += 1
      end

      #取出title 與 this_amount放入result
      #.to_s >> 將this_amount轉成字串
      result += "\t" + element.movie.title + "\t" + this_amount.to_s + "\n"
      total_amount += this_amount
    end

    result += "Amount owed is #{total_amount}\n"
    result += "You earned #{frequent_renter_points} frequent renter points"
    result
  end
end

####

#轉換Movie::??的值
#將title 與 price_code指定給 @title @price_code
class Movie
  REGULAR     = 0
  NEW_RELEASE = 1
  CHILDRENS   = 2

  attr_reader :title
  attr_accessor :price_code

  def initialize(title, price_code)
    @title, @price_code = title, price_code
  end
end

####

#將movie 與 days_rented指定給 @movie @days_rented
class Rental
  attr_reader :movie, :days_rented

  def initialize(movie, days_rented)
    @movie, @days_rented = movie, days_rented
  end
end

####

#此程式為計算租片點數，和記錄eddie這個使用者的租片紀錄
#將Customer實體化與執行，並給client這個物件
client = Customer.new('eddie')

#執行Movie類別，並將參數丟入
movie1 = Movie.new('ruby', Movie::NEW_RELEASE)

#執行Rental類別，並將attr_reader::title 與參數丟入
rental1 = Rental.new(movie1, 3)

#執行Customer裡的add_rental 方法
client.add_rental rental1

movie2 = Movie.new('php', Movie::REGULAR)
rental2 = Rental.new(movie2, 7)
client.add_rental rental2

puts client.statement

#每個Class具有獨立的程式邏輯，達到模組化的效果，Customer類別是作為計算點數與儲存紀錄，Movie是將電影名稱丟入變數和定義代碼，Rental是建立電影名稱和租借時間
