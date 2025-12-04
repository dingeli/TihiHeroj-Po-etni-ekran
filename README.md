# TihiHeroj-Pocetni-ekran
Početni ekran aplikacije tihi heroj, vezan za prijavu nasilja. Odradjeni deo predstavlja početni ekran, sastavljen od tri kartice na kojima se nalazi slika, tekst, i koje mogu da se listaju. Takodje, postoji dugme kojim se prelazi na novi ekran i dugme istraži kojim se takodje prelazi na novi ekran.



import SwiftUI

struct CardData: Identifiable {
    var id = UUID()
    var image: String
    var title: String
    var text: String
}

    struct WelcomeViewCards: View {
        var card: CardData
        var body: some View {
            VStack(spacing:0){
                Image(card.image)
                    .frame(width: 337, height: 337)
                    .padding(.top,-30)
                VStack(alignment: .leading, spacing: 4)
                {
                    Text(card.title)
                        .font(.system(size:22, weight: .semibold))
                        .lineSpacing(6.6)
                        .kerning(-0.4)
                        .foregroundColor(Color("cardwelcome"))
                        .padding(15)
                        .padding(.top,10)
                    Text(card.text)
                        .foregroundColor(Color("cardwelcometext"))
                        .padding(.horizontal, 15)
                    
                }
            }
            .frame(width:337,height:550)
            .background(.white)
            .clipShape(RoundedRectangle(cornerRadius: 20))
            .shadow(color:  Color.black.opacity(0.1),
                    radius: 15, x: 0, y: 10)
        }
    }
    struct CardsCarouselView: View {
    let cards: [CardData] = [
        CardData(image: "card1", title: "Пријави, не ћути", text: "Твоја безбедност је наш приоритет. Користи једноставан формулар за пријаву вршњачког насиља. Ми обезбеђујемо поверљивост, а твој глас може направити разлику."),
        CardData(image: "card2", title: "Заштити себе", text: "Поступци и савети за безбедност..."),
        CardData(image: "card3", title: "Помози другима", text: "Како да помогнеш вршњацима у проблему...")
    ]
    
    @State private var currentIndex: Int = 0
    
    var body: some View {
        GeometryReader { proxy in
            let screenWidth = proxy.size.width
            VStack {
                ScrollView(.horizontal, showsIndicators: false) {
                    HStack(spacing: 20) {
                        ForEach(Array(cards.enumerated()), id: \.element.id) { index, card in
                            GeometryReader { geo in
                                WelcomeViewCards(card: card)
                                    .scaleEffect(scaleForCard(geo: geo, screenWidth: screenWidth))
                                    .animation(.spring(), value: currentIndex)
                                    .onChange(of: geo.frame(in: .global).midX) { oldValue, newValue in
                                        let screenCenter = screenWidth / 2
                                        if abs(newValue - screenCenter) < 150 {
                                            currentIndex = index
                                        }
                                    }
                            }
                            .frame(width: 300, height: 550)
                        }
                    }
                    .padding(.horizontal, (screenWidth - 300)/2)
                }
                .frame(height: 550)
                
                // Page dots
                HStack(spacing: 8) {
                    ForEach(0..<cards.count, id: \.self) { index in
                        Circle()
                            .fill(currentIndex == index ? Color.purple : Color.gray.opacity(0.3))
                            .frame(width: 10, height: 10)
                            .animation(.easeInOut, value: currentIndex)
                    }
                }
                .padding(.top, 10)
            }
        }
        .frame(height: 600)
    }
    
    private func scaleForCard(geo: GeometryProxy, screenWidth: CGFloat) -> CGFloat {
        let cardCenter = geo.frame(in: .global).midX
        let screenCenter = screenWidth / 2
        let distance = abs(screenCenter - cardCenter)
        let maxDistance: CGFloat = 200
        let minScale: CGFloat = 0.9
        let scale = max(minScale, 1 - (distance / maxDistance) * 0.1)
        return scale
    }
}
