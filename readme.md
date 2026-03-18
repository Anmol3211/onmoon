import { useState, useRef, useEffect, useCallback } from "react";
import FilterPills from "@/components/FilterPills";
import SignalCardView from "@/components/SignalCardView";
import ActionBar from "@/components/ActionBar";
import { SIGNAL_DATA, CardType } from "@/data/signals";

const Index = () => {
  const [filter, setFilter] = useState<CardType | "ALL">("ALL");
  const [activeIndex, setActiveIndex] = useState(0);
  const containerRef = useRef<HTMLDivElement>(null);

  const filteredCards = filter === "ALL"
    ? SIGNAL_DATA
    : SIGNAL_DATA.filter((c) => c.type === filter);

  // Reset scroll and active index when filter changes
  useEffect(() => {
    setActiveIndex(0);
    if (containerRef.current) {
      containerRef.current.scrollTo({ top: 0 });
    }
  }, [filter]);

  const handleScroll = useCallback(() => {
    const el = containerRef.current;
    if (!el) return;
    const idx = Math.round(el.scrollTop / el.clientHeight);
    setActiveIndex(idx);
  }, []);

  return (
    <div className="h-[100dvh] w-full flex flex-col bg-background overflow-hidden">
      {/* Header */}
      <div className="shrink-0 border-b border-border z-40 bg-background">
        <div className="flex items-center justify-between px-4 pt-3 pb-1">
          <h2 className="font-extrabold text-lg tracking-tight text-onmoon-cream">
            ONMOON
          </h2>
          <span className="font-mono text-[10px] text-onmoon-grey tracking-wider">
            {filteredCards.length} SIGNALS
          </span>
        </div>
        <FilterPills active={filter} onChange={setFilter} />
      </div>

      {/* Feed */}
      <div
        ref={containerRef}
        onScroll={handleScroll}
        className="flex-1 overflow-y-scroll snap-y snap-mandatory snap-container"
      >
        {filteredCards.length === 0 ? (
          <div className="h-[100dvh] flex items-center justify-center">
            <p className="font-mono text-sm text-onmoon-grey">No signals for this filter.</p>
          </div>
        ) : (
          filteredCards.map((card, i) => (
            <SignalCardView key={card.id} card={card} isActive={i === activeIndex} />
          ))
        )}
      </div>

      {/* Action bar */}
      <ActionBar />
    </div>
  );
};

export default Index;
